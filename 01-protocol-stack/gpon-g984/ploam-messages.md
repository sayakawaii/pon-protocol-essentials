# PLOAM 消息

> PLOAM（Physical Layer OAM）是 PON 物理层的带内信令，承载 ONU 发现、ONU-ID 分配、测距、加密、功率管理等控制流程。本篇给出下行/上行 PLOAM 消息全集与角色；字段级编码待后续补充。

## 1. PLOAM 概述

- PLOAM 消息在下行帧的 PLOAM 分区、上行突发的 PLOAMu 区传输。
- **GPON（G.984.3）**：PLOAM 消息 **13 字节**；**XGS-PON（G.9807.1）/ NG-PON2（G.989.3）**：**48 字节**，带基于 PLOAM-IK 的 MIC 完整性校验。
- 每条消息有 Message-ID、ONU-ID、SeqNum（用于 Ack/重传，G.989.3 §11.3.5）。

## 2. 下行 PLOAM 消息（OLT → ONU）

| Msg-ID | 消息 | 作用 |
|--------|------|------|
| 0x01 | Burst_Profile | 下发上行突发参数（前导、定界、FEC） |
| 0x03 | Assign_ONU-ID | 分配 ONU-ID（SN 匹配） |
| 0x04 | Ranging_Time | 下发均衡时延 EqD |
| 0x05 | Deactivate_ONU-ID | 去激活 ONU |
| 0x06 | Disable_Serial_Number | 禁用/启用指定 SN（→ O7 / O7→O1） |
| 0x09 | Request_Registration | 索要 Registration |
| 0x0A | Assign_Alloc-ID | 分配 Alloc-ID（绑定 T-CONT） |
| 0x0D | Key_Control | 加密密钥控制 |
| 0x12 | Sleep_Allow | 允许 ONU 进入省电 |
| 0x13 | Calibration_Request | 校准请求 |
| 0x14 | Adjust_Tx_Wavelength | 调整发送波长（TWDM） |
| 0x15 | Tuning_Control | 波长调谐控制 |
| 0x17 | System_Profile | 系统 profile |
| 0x18 | Channel_Profile | 通道 profile |
| 0x19 | Protection_Control | 保护倒换控制 |
| 0x1A | Change_Power_Level | 改变发送功率 |
| 0x1B | Power_Consumption_Inquiry | 功耗查询 |
| 0x1C | Rate_Control | 速率控制 |
| 0x1D | Reboot_ONU | 重启 ONU |

> 上表的 Message-ID 与 `gopon/common/ploam/ids.go` 的 `DsMsgID` 常量一一对应。注意 GPON（G.984.3）与 XGS-PON（G.9807.1）的消息集合略有差异：上表为合并视图，部分消息（波长调谐、System/Channel Profile）仅用于 10G/TWDM 体系。

## 3. 上行 PLOAM 消息（ONU → OLT）

| Msg-ID | 消息 | 作用 |
|--------|------|------|
| 0x01 | Serial_Number_ONU | 上报 ONU 序列号（O2-3） |
| 0x02 | Registration | 注册（O4，携带 Registration-ID） |
| 0x05 | Key_Report | 上报加密密钥 |
| 0x09 | Acknowledge | 确认下行 PLOAM |
| 0x10 | Sleep_Request | 请求进入省电 |
| 0x1A | Tuning_Response | 波长调谐响应 |
| 0x1B | Power_Consumption_Report | 上报功耗/功率等级 |
| 0x1C | Rate_Response | 速率控制响应 |

> 对应 `gopon/common/ploam/ids.go` 的 `UsMsgID` 常量。

## 4. 在激活流程中的角色

PLOAM 消息驱动了整个激活状态机，详见 [激活状态机 ⭐](activation-state-machine.md) 第 6 节。

## 5. GPON 13 字节 PLOAM 布局

GPON 的 PLOAM 消息固定 **13 字节**（G.984.3 §9.2）：

```
Octet:  1        2        3 ............... 12        13
       +--------+--------+--------------------+--------+
       | ONU-ID | Msg-ID |   Message content   |  CRC   |
       | (1B)   | (1B)   |   (10B)             |  (1B)  |
       +--------+--------+--------------------+--------+
```

| Octet | 字段 | 说明 |
|-------|------|------|
| 1 | ONU-ID | 8-bit（`0xFF` 广播；GPON ONU-ID 范围 0..253） |
| 2 | Message-ID | 消息类型（见第 2/3 节） |
| 3-12 | Message content | 10 字节消息内容 |
| 13 | CRC | CRC-8 校验 |

> 与 XGS-PON 的关键差异：GPON 用 **8-bit ONU-ID + CRC**；XGS-PON 用 **10-bit ONU-ID（2 字节）+ 8 字节 MIC**。XGS-PON 48B 字段级编码见 [XGS-PON PLOAM 消息](../xgspon-g9807/ploam-messages.md)。

## 6. 工程实现佐证

`gopon` 的 PLOAM Frame 把 SeqNum、Direction、MsgID、Payload 统一建模，便于上层处理两代格式：

```30:34:/home/mingheh/project/gopon/common/ploam/frame.go
	// SeqNum is the sequence number used for Ack/retransmit tracking (G.989.3 clause 11.3.5).
	SeqNum uint8 `json:"seq_num"`
```

## 来源

- **公有标准**：ITU-T G.984.3 §9.2（GPON PLOAM 消息：13 字节布局 ONU-ID/Msg-ID/content/CRC、消息集合）。XGS-PON 48B 字段级编码见 [XGS-PON PLOAM 消息](../xgspon-g9807/ploam-messages.md)（依据 G.9807.1 §C.11）。
- **工程实现**：`gopon/common/ploam/ids.go`（DsMsgID / UsMsgID 全集 + String()）、`gopon/common/ploam/frame.go`（Frame 结构、SeqNum、Direction）。
