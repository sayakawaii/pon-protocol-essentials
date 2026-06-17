# EPON / 10G-EPON 概览（对照）

> IEEE 路线的 EPON（802.3ah）与 10G-EPON（802.3av）。本篇做简略对照，帮助横向理解 IEEE 与 ITU 两条 PON 技术路线的异同。

## 1. 与 ITU 路线的根本区别

- **封装**：EPON 直接传**原生以太网帧**，无 GEM/XGEM 封装。
- **多址 / 带宽授权**：用 **MPCP（Multi-Point Control Protocol，802.3 Clause 64/77）** 的 **GATE / REPORT** 消息，而非 BWmap / DBRu。
- **管理**：用 **OAM（802.3 Clause 57）**，而非 OMCI。
- **逻辑链路标识**：用 **LLID（Logical Link ID）**，而非 GEM Port-ID / Alloc-ID。

## 2. 概念映射（速记）

| 功能 | EPON | ITU (GPON/XGS-PON) |
|------|------|--------------------|
| 逻辑链路 | LLID | GEM Port-ID / Alloc-ID |
| 上行授权 | GATE | BWmap |
| 缓存上报 | REPORT | DBRu / Status Report |
| 发现/测距 | MPCP Discovery（REGISTER_REQ/REGISTER） | Ranging（Assign_ONU-ID / Ranging_Time） |
| 管理 | OAM | OMCI |

## 3. MPCP 发现与授权（TODO）

- [ ] MPCP Discovery handshake：DISCOVERY GATE → REGISTER_REQ → REGISTER → REGISTER_ACK
- [ ] GATE：授权 grant（start time + length）
- [ ] REPORT：上报队列状态
- [ ] RTT 测量与时间戳同步

## 4. 速率

| 技术 | 标准 | 速率 |
|------|------|------|
| EPON | IEEE 802.3ah | 1G 对称 |
| 10G-EPON | IEEE 802.3av | 10G 对称 / 10G-1G 非对称 |
| 25/50G-EPON | IEEE 802.3ca | 25 / 50 Gbit/s |

## 来源（待检索补全）

- IEEE 802.3ah（EPON）、802.3av（10G-EPON）、802.3ca（25/50G-EPON）；MPCP（802.3 Clause 64/77）、OAM（Clause 57）。
