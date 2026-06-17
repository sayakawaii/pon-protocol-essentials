# OMCI 白盒：代码走读（gopon ME 模型与编解码）

> 把 [OMCI 规范](omci-spec.md) 与 [ME 参考](me-reference.md) 落到 gopon 的真实数据结构：ME 规格如何建模、属性掩码/访问权限如何表达、哪些 ME 自动创建。读完能把 G.988 §9 的 ME 表对上代码。

> 代码索引见 [code-map](../_sources/code-map.md)；DBA 侧白盒见 [DBA 代码走读](../03-dba/code-walkthrough.md)。

## 1. 属性类型与访问权限 ↔ G.988

gopon 用 `AttrType` 表达属性的线格式，`AttrAccess` 表达读写/创建权限——直接对应 G.988 每个属性后括注的 `(R)/(W)/(set-by-create)` 与字节长度：

```5:24:gopon/common/omci/me_spec.go
// AttrType is a coarse classification of attribute storage / wire format.
type AttrType string

const (
	AttrUint8   AttrType = "uint8"
	AttrUint16  AttrType = "uint16"
	AttrUint32  AttrType = "uint32"
	AttrUint64  AttrType = "uint64"
	AttrString  AttrType = "string"   // null-padded fixed length
	AttrBytes   AttrType = "bytes"    // fixed-length octet string
	AttrTable   AttrType = "table"    // table attribute (GetNext / SetTable)
	AttrPointer AttrType = "pointer"  // 16-bit pointer to another ME instance
)

// AttrAccess describes what operations the attribute permits.
type AttrAccess struct {
	Read   bool
	Write  bool
	SetByCreate bool
}
```

- `AttrTable` ↔ 表属性，靠 **Get Next / Set Table** 操作（如 [VLAN 处理表](vlan-qos-modeling.md)、[组播 ACL](multicast-control.md)）；
- `AttrPointer` ↔ 16-bit 指针，ME 之间靠指针串成数据通道（见 [datapath 全景](datapath-l2-model.md)）。

## 2. 属性号 ↔ Attribute Mask 的位

```26:34:gopon/common/omci/me_spec.go
// AttrSpec describes a single attribute of a ME.
type AttrSpec struct {
	Name     string
	Index    uint8 // 1-based attribute number; bit set in AttrMask
	Type     AttrType
	Length   int   // bytes (for fixed-length); 0 means variable
	Access   AttrAccess
	Default  any
}
```

- `Index` 是 **1-based 属性号**，正是 OMCI 消息里 **Attribute Mask** 置位的那一位（见 [消息示例](message-examples.md)）：Get/Set 用掩码选择操作哪些属性。属性号 1 = 掩码最高位。

## 3. ME 规格与「自动创建」语义

```36:48:gopon/common/omci/me_spec.go
// MESpec describes a Managed Entity class (G.988 clause 9).
type MESpec struct {
	Class       ClassID
	Name        string
	Description string
	Attributes  []AttrSpec
	// AutoCreate is true for MEs the ONU creates itself at boot (e.g. ONT-G,
	// ANI-G, UNI-G); set false for MEs that must be explicitly created by the
	// OLT/vOMCI (e.g. T-CONT, GEM Port Network CTP).
	AutoCreate bool
	// MaxInstances is the maximum number of instances (0 = unlimited).
	MaxInstances int
}
```

- `AutoCreate` 区分两类 ME：
  - **ONU 自建**（ONU-G、ANI-G、UNI-G、T-CONT 容器等）：随设备发现自动出现，OLT 只能 Get/Set，不能 Create/Delete；
  - **OLT 显式创建**（GEM Port Network CTP、GAL、Bridge 等）：开通业务时由 OLT/vOMCI `Create`。
- 这正是 [MIB 上传同步](mib-upload-sync.md) 与 [HSI 开通](provisioning-hsi.md) 流程里「先看 ONU 自建了什么，再 Create 缺的」的判据。

## 4. 注册表：按 ClassID 索引 ME

```50:54:gopon/common/omci/me_spec.go
// registry indexes ME specs by ClassID.
var (
	registryMu sync.RWMutex
	registry   = map[ClassID]MESpec{}
)
```

- `me_g988.go` 在 init 期把 G.988 各 ME 注册进 `registry`，解码 OMCI 时按消息里的 **class id**（第 5–6 字节，见 [消息示例](message-examples.md)）查规格 → 知道每个属性的类型/长度 → 正确切分 Message contents。这就是「ME 表驱动的编解码器」。

## 5. PLOAM 侧（gopon `common/ploam`）

- `ploam/ids.go`：下行/上行 PLOAM 消息 ID 枚举（`DsMsgID`/`UsMsgID`），对应 [PLOAM 消息](../01-protocol-stack/xgspon-g9807/ploam-messages.md) 的消息类型字段；
- `ploam/frame.go`：PLOAM 帧编解码（XGS-PON 48B：ONU-ID + MsgType + SeqNo + 内容 + MIC）；
- `ploam/codec_json.go`：把二进制 PLOAM 转 JSON，便于 [抓包排障](../05-operations/troubleshooting.md) 与可视化。

## 6. 白盒小结：表驱动的好处

| G.988 概念 | gopon 代码 | 好处 |
|------------|-----------|------|
| ME 类（§9） | `MESpec` + `registry` | 加新 ME 只填表，不改解码器 |
| 属性 (R)(W)(长度) | `AttrSpec{Access,Type,Length}` | 编解码/校验统一 |
| 属性号 ↔ 掩码位 | `Index` (1-based) | Get/Set 掩码直接映射 |
| 自建 vs 显式创建 | `AutoCreate` | 开通流程判「该不该 Create」 |
| 指针关系 | `AttrPointer` | 串起 datapath |

> 「表驱动 + 指针拓扑」是 OMCI 白盒的精髓：把协议复杂度收敛进**数据**（ME 表），编解码与开通逻辑保持稳定——这也是看任意厂商 OMCI 栈的通用心法。

## 来源

- **代码**（工作区）：
  - `gopon/common/omci/me_spec.go`（`AttrType`/`AttrAccess`/`AttrSpec`/`MESpec`/`registry`）。
  - `gopon/common/omci/me_g988.go`（G.988 ME 注册表）。
  - `gopon/common/ploam/`（`ids.go` 消息 ID、`frame.go` 帧编解码、`codec_json.go`）。
- **公有标准**：ITU-T G.988 §9（ME 类与属性、(R)/(W)/(set-by-create)、属性号与 Attribute Mask）、§11.2（消息格式）。
- 说明：gopon 为教学/模拟实现，ME 覆盖度以 `me_g988.go` 为准；权威定义以 G.988 原文为准。
