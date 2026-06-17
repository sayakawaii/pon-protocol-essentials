# 协议栈深度梳理

> 系统性整理 GPON / XG-PON / XGS-PON / EPON 的状态机、帧结构、PLOAM 消息。重点 GPON 与 XGS-PON，XG-PON 写差异，EPON 做对照。

## 分层视角

PON（ITU 路线）的 TC 层自下而上：物理同步（PSBd/PSBu）→ 成帧（GTC/XGTC/FS）→ 封装（GEM/XGEM）→ 信令（PLOAM）与调度（DBA/BWmap）。管理面 OMCI 跑在专用 OMCC GEM Port 上。

## 目录

### GPON (ITU-T G.984.x)

- [激活状态机 ⭐](gpon-g984/activation-state-machine.md) — O1→O5 全流程、PLOAM 消息、定时器
- [帧结构](gpon-g984/frame-structure.md) — GTC 下行/上行帧、GEM 封装（提纲）
- [PLOAM 消息](gpon-g984/ploam-messages.md) — 下行/上行消息全集
- [测距与激活](gpon-g984/ranging-activation.md) — Ranging、EqD（提纲）

### XGS-PON (ITU-T G.9807.1)

- [激活状态机](xgspon-g9807/activation-state-machine.md) — O1-O7（O2-3 合并）（提纲）
- [帧结构](xgspon-g9807/frame-structure.md) — XGTC / FS / PSBd（提纲）
- [PLOAM 消息](xgspon-g9807/ploam-messages.md) — 48 字节 PLOAM（提纲）

### XG-PON (ITU-T G.987.x)

- [与 XGS-PON 的差异](xgpon-g987/delta-vs-xgspon.md) — 主要差异（提纲）

### EPON / 10G-EPON (IEEE 802.3ah / 802.3av)

- [概览（对照）](epon-10gepon/overview.md) — MPCP / LLID / OAM（简略）

## 阅读建议

先吃透 [GPON 激活状态机 ⭐](gpon-g984/activation-state-machine.md)，再看 XGS-PON 差异，效率最高 —— 两者 TC 层高度同源。
