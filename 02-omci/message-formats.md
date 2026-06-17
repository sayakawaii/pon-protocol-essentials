# OMCI 消息格式：Baseline 与 Extended（逐字段）

> OMCI 有两种线格式：**Baseline（基线）固定 48 字节**（历史主流），**Extended（扩展）变长**（一条消息可携带更多内容，减少往返）。本篇逐字段对照两种格式的布局、字段语义与适用场景。依据 G.988 §11.2。

> 操作类型（Create/Set/Get…）、MIC、MIB 同步见 [OMCI 规范总览](omci-spec.md)；本篇专注**帧布局**。

## 1. 封装位置

每个 OMCI 包封装进**一个 GEM/XGEM 帧**（必要时按正常分片规则跨多个帧），GEM 头携带 **OMCC Port-ID**（见 [GEM/XGEM 封装](../01-protocol-stack/gem-xgem/encapsulation-delineation.md)）。

```mermaid
flowchart LR
    OMCI["OMCI 包 (baseline 48B / extended 变长)"] --> GEM["GEM/XGEM 封装\n(OMCC Port-ID)"] --> TC["XGTC/GTC 帧"] --> PHY["PON 物理层"]
```

## 2. Baseline 格式（Table 11.2-1，固定 48 字节）

| 字节 | 大小 | 字段 |
|------|------|------|
| 1–2 | 2 | **Transaction Correlation Identifier (TCI)** — 关联请求/响应 |
| 3 | 1 | **Message Type** — 含 DB/AR/AK 位 + action（见下） |
| 4 | 1 | **Device Identifier** — baseline = `0x0A` |
| 5–8 | 4 | **Managed Entity Identifier** — Class ID(2B) + Instance ID(2B) |
| 9–40 | 32 | **Message Contents** — 操作相关（属性掩码 + 属性值等） |
| 41–48 | 8 | **OMCI Trailer** — 含 MIC（CRC-32）等 |

特点：**消息内容固定 32 字节**——大属性/表必须靠 **Get Next / MIB Upload Next** 分多条传输（见 [MIB 同步](mib-upload-sync.md)）。

## 3. Extended 格式（Table 11.2-2，变长 N ≤ 1980 字节）

| 字节 | 大小 | 字段 |
|------|------|------|
| 1–2 | 2 | **Transaction Correlation Identifier (TCI)** |
| 3 | 1 | **Message Type** |
| 4 | 1 | **Device Identifier** — extended 用不同取值标识 |
| 5–8 | 4 | **Managed Entity Identifier** |
| 9–10 | 2 | **Message Contents Length** — 内容长度（baseline 没有此字段） |
| 11 … (N−4) | 变长 | **Message Contents** |
| (N−3) … N | 4 | **Message Integrity Check (MIC)** |

特点：
- 新增 **Message Contents Length（2B）**，使内容**变长**；
- 一条消息可携带**多个属性 / 多个 ME 实例**，显著减少 Get Next / Upload Next 的往返次数；
- EPON 上的扩展 OMCI（封进以太扩展 OAM 帧）最长 **1493 字节**（见 [EPON](../01-protocol-stack/epon-10gepon/overview.md)）。

## 4. 两格式对照

| 维度 | Baseline | Extended |
|------|----------|----------|
| 总长 | 固定 48B | 变长，≤1980B（EPON ≤1493B） |
| 内容长度字段 | 无（内容恒 32B） | 有（9–10 字节） |
| MIC 位置 | trailer 内（41–48） | 末 4 字节 |
| 大数据传输 | 多条 Get Next / Upload Next | 一条可带多项，往返少 |
| 兼容性 | 历史最广 | 新设备/大 MIB 更高效 |

## 5. Message Type 字节（byte 3）的位结构

```
 bit: 8    7    6    5 4 3 2 1
     +----+----+----+-----------+
     | DB | AR | AK |  action   |
     +----+----+----+-----------+
```

| 位 | 含义 |
|----|------|
| **DB**（Destination/DB bit） | 高位标志（baseline=0） |
| **AR**（Acknowledge Request） | =1 期望响应 |
| **AK**（Acknowledgement） | =1 本消息是响应 |
| **bits 5–1 action** | 操作码（Create=4、Delete=6、Set=8、Get=9、MIB Upload Next、Activate Image…） |

例（来自 G.988 附录 A）：
- **MIB Upload Next**：DB=0, AR=1, AK=0（请求）。
- **Activate/Commit Image 响应**：DB=0, AR=0, AK=1。

## 6. 工程要点

- **TCI 配对**：OLT 用 TCI 把响应对回请求；高并发下 TCI 池管理影响吞吐。
- **选格式**：ONU 通过能力属性声明是否支持 extended；OLT 据此选择，混用时按 Device Identifier 区分。
- **互通**：TR-309 「OMCI Channel Establishment」用例验证 OLT/ONU 能正确收发 **baseline 48 字节 OMCI SDU** 的数据图案（见 [互通测试](../06-interop/test-plan-overview.md)）。

## 来源

- **公有标准**：
  - ITU-T G.988 (2024) §11.2（Common message characteristics：OMCI 封进 GEM、OMCC Port-ID）、Table 11.2-1（Baseline 48B 逐字段：TCI/Message type/Device id/ME id/Message contents 32B/Trailer 8B）、Table 11.2-2（Extended 变长 N≤1980B：新增 Message contents length 2B，MIC 末 4B）、§11.2.1–11.2.8（各字段定义）。
  - Device identifier baseline=0x0A；Message type 位 DB/AR/AK + action（附录 A 各消息）。
  - Annex C.2（EPON extended OMCI ≤1493B）。
- **工程实现**：`gopon/common/omci/` 的消息 codec。
- 说明：Message Type 位结构与 action 取值为 G.988 附录 A 的归纳；逐操作码全集以原文为准。
