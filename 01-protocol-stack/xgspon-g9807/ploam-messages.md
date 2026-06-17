# XGS-PON PLOAM 消息（字段级）

> XGS-PON / NG-PON2 的 PLOAM 消息固定 **48 字节**，带基于 PLOAM_IK 的 **MIC** 完整性校验（G.9807.1 §C.11 / G.989.3 §11）。本篇给出通用布局、MIC 计算、SeqNo/Ack 机制，以及几条关键消息的字段表。

> 下行/上行消息**列表与角色**见 [GPON PLOAM 消息](../gpon-g984/ploam-messages.md)（合并视图）；本页聚焦 **48B 字段级编码**。

## 1. 通用 48 字节布局

所有 XGS-PON PLOAM 消息共用统一帧头与 MIC 尾（G.9807.1 §C.11.3）：

```
Octet:  1   2   3        4      5 .................. 40     41 ........ 48
       +-------+--------+------+------------------------+----------------+
       | ONU-ID| MsgType| SeqNo|   Message content       |      MIC       |
       | (2B)  | ID(1B) | (1B) |   / Padding (36B)       |     (8B)       |
       +-------+--------+------+------------------------+----------------+
```

| Octet | 字段 | 说明 |
|-------|------|------|
| 1-2 | ONU-ID | 目标/源 ONU-ID。`0x03FF` = 广播；定向消息为具体 ONU-ID |
| 3 | Message type ID | 消息类型（见 [消息列表](../gpon-g984/ploam-messages.md)） |
| 4 | SeqNo | PLOAM 序列号（广播/单播；用于 Ack 配对） |
| 5-40 | Message content / Padding | 消息特定内容；未用部分填 `0x00`，接收端 "don't care" |
| 41-48 | MIC | Message Integrity Check（8 字节） |

## 2. MIC 完整性校验

- MIC（Octet 41-48）覆盖整条消息，用 **PLOAM_IK（PLOAM Integrity Key）** 计算（CMAC 类）。
- **广播消息**：使用 **default PLOAM_IK**。
- **定向消息**：使用 **ONU-specific derived shared PLOAM_IK**（激活/认证后派生的 per-ONU 密钥）。

> 这是与 GPON（13B、CRC 校验）的关键安全升级：MIC 防止 PLOAM 信令被伪造/篡改。

## 3. SeqNo 与 Acknowledge

- OLT 下行 PLOAM 带 SeqNo；ONU 用 **Acknowledgement 消息**（上行）回 `SeqNo = 下行序列号`，并带 Completion code。
- ONU 无上行消息可发（OLT 的 keep-alive grant）时，上行 SeqNo 置 `0x00`。
- Completion code（Acknowledgement，Octet 5）：

| 值 | 含义 |
|----|------|
| 0x00 | OK |
| 0x01 | No message to send |
| 0x02 | （保留/参数相关） |
| 0x03 | Busy, preparing a response |
| 0x04 | Unknown message type |
| 0x05 | Parameter error / Processing error |

## 4. 关键消息字段表

### Assign_ONU-ID（下行，Table C.11.6，MsgType 0x03）

| Octet | 内容 | 说明 |
|-------|------|------|
| 1-2 | ONU-ID | `0x03FF` 广播 |
| 3 | Message type ID | `0x03` |
| 4 | SeqNo | 8-bit 广播 PLOAM 序列号 |
| 5-6 | ONU-ID | LSB 对齐的 10-bit 分配值，高 6 bit 补零；范围 0..1020（0x0000..0x03FC） |
| 7-10 | Vendor_ID | 见 C.11.2.6.1 |
| 11-14 | VSSN | 见 C.11.2.6.2 |
| 15 | Upstream nominal line rate | `0000 000U`：U=0 → 2.5 Gbit/s，U=1 → 10 Gbit/s |
| ... | Padding | `0x00` |
| 41-48 | MIC | default PLOAM_IK |

### Ranging_Time（下行，Table C.11.7，→ O5）

| Octet | 内容 | 说明 |
|-------|------|------|
| 1-2 | ONU-ID | 定向 |
| 3 | Message type ID | `0x04` |
| 4 | SeqNo | 单播序列号 |
| 5... | EqD | 均衡时延（绝对/相对，由解析方式字节指示） |
| 14-17 | Upstream PON-ID | XGS-PON 不用，置 `0x00` |
| 18-40 | Padding | `0x00` |
| 41-48 | MIC | 广播用 default、定向用 derived PLOAM_IK |

### Request_Registration（下行，Table C.11.10，MsgType 0x09）

| Octet | 内容 | 说明 |
|-------|------|------|
| 1-2 | ONU-ID | 定向 |
| 3 | Message type ID | `0x09` |
| 4 | SeqNo | 8-bit 单播序列号 |
| 5-40 | Padding | `0x00` |
| 41-48 | MIC | — |

### Serial_Number_ONU（上行，Table C.11.24，MsgType 0x01）

| Octet | 内容 | 说明 |
|-------|------|------|
| 1-2 | ONU-ID | `0x03FF` Unassigned |
| 3 | Message type ID | `0x01` |
| 4 | SeqNo | 所有实例置 `0x00` |
| 5-8 | Vendor_ID | 见 C.11.2.6.1 |
| 9-12 | VSSN | 见 C.11.2.6.2 |
| 13-16 | Random_delay | ONU 发送本消息所用随机延迟（以 2.48832 Gbit/s 标称速率的整数比特周期计） |

### Key_Report（上行，Table C.11.26，MsgType 0x05）

| Octet | 内容 | 说明 |
|-------|------|------|
| 1-2 | ONU-ID | 源 ONU |
| 3 | Message type ID | `0x05` |
| 4 | SeqNo | 重复下行 Key_Control 的值（多片时相同） |
| 5 | Report type | `R`：0=新密钥，1=对现有密钥的报告 |
| 6 | Key index | `BB`：01=密钥对第一把，10=第二把 |
| 7 | Fragment number | 3-bit 片号 0..7（末片可部分，低位补 `0x00`） |
| 9-40 | Key_Fragment | 密钥片，32 字节（高字节补齐） |

### Acknowledgement（上行，Table C.11.27，MsgType 0x09）

见第 3 节 Completion code。Octet 6/7（Attenuation / Power levelling）XGS-PON 不用，置 `0x00`；8-40 Padding。

## 5. 工程实现佐证

`gopon` 的 PLOAM Frame 模型显式建模了 SeqNum（用于 Ack/重传）与方向：

```30:34:/home/mingheh/project/gopon/common/ploam/frame.go
	// SeqNum is the sequence number used for Ack/retransmit tracking (G.989.3 clause 11.3.5).
	SeqNum uint8 `json:"seq_num"`
```

消息 ID 全集见 `gopon/common/ploam/ids.go`（`DsMsgID` / `UsMsgID`）。

## 6. 与 GPON 13B PLOAM 的差异

| 维度 | GPON (G.984.3) | XGS-PON (G.9807.1) |
|------|----------------|--------------------|
| 长度 | 13 字节 | 48 字节 |
| 完整性 | CRC | MIC（PLOAM_IK，CMAC） |
| ONU-ID | 1 字节（8-bit，0xFF 广播） | 2 字节（10-bit，0x03FF 广播） |
| Ack 机制 | 隐式 | 显式 Acknowledgement + SeqNo |

GPON 13B 布局见 [GPON PLOAM 消息](../gpon-g984/ploam-messages.md) 第 5 节。

## 来源

- **公有标准**：ITU-T G.9807.1 (2023) §C.11.3：通用 48B 布局（ONU-ID/MsgType/SeqNo/content/MIC）；Table C.11.6（Assign_ONU-ID）、C.11.7（Ranging_Time）、C.11.8（Deactivate）、C.11.9（Disable_SN）、C.11.10（Request_Registration）、C.11.24（Serial_Number_ONU）、C.11.26（Key_Report）、C.11.27（Acknowledgement）；MIC 用 default / derived PLOAM_IK；SeqNo/Completion code。G.989.3 §11.3.5（SeqNum/Ack/重传）。
- **工程实现**：`gopon/common/ploam/frame.go`（Frame.SeqNum）、`ploam/ids.go`（消息 ID）。
