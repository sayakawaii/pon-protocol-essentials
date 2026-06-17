# 协议栈深度梳理

> 系统性整理 GPON / XG-PON / XGS-PON / EPON 的状态机、帧结构、PLOAM 消息。重点 GPON 与 XGS-PON，XG-PON 写差异，EPON 做对照。

## 分层视角

PON（ITU 路线）的 TC 层自下而上：物理同步（PSBd/PSBu）→ 成帧（GTC/XGTC/FS）→ 封装（GEM/XGEM）→ 信令（PLOAM）与调度（DBA/BWmap）。管理面 OMCI 跑在专用 OMCC GEM Port 上。

## 目录

### GPON (ITU-T G.984.x)

- [激活状态机 ⭐](gpon-g984/activation-state-machine.md) — O1→O5 全流程、PLOAM 消息、定时器
- [帧结构](gpon-g984/frame-structure.md) — GTC 下行/上行帧、GEM 封装、BWmap、FEC
- [PLOAM 消息](gpon-g984/ploam-messages.md) — 下行/上行消息全集 + 13B 布局
- [测距与激活](gpon-g984/ranging-activation.md) — Ranging、EqD、静默窗、Drift/DOW/TIW

### XGS-PON (ITU-T G.9807.1)

- [激活状态机](xgspon-g9807/activation-state-machine.md) — O1-O7、子状态、下行同步机
- [帧结构](xgspon-g9807/frame-structure.md) — XGTC / FS / PSBd / XGEM / RS(248,216)
- [PLOAM 消息](xgspon-g9807/ploam-messages.md) — 48 字节 PLOAM 字段级 + MIC

### XG-PON (ITU-T G.987.x)

- [与 XGS-PON 的差异](xgpon-g987/delta-vs-xgspon.md) — TC 层逐项对照、PMD/ODN 差异

### NG-PON2 (ITU-T G.989.x)

- [NG-PON2 与 TWDM 波长调谐](ngpon2-g989/overview.md) — TWDM 架构、ONU 调谐状态机、ICTP

### 25/50G-PON (ITU-T G.9804.x, HSP)

- [25/50G-PON 概览](hsp-g9804/overview.md) — 速率/LDPC FEC、功率调平、与 XGS-PON 差异

### EPON / 10G-EPON (IEEE 802.3ah / 802.3av)

- [概览（对照）](epon-10gepon/overview.md) — MPCP / GATE / REPORT / LLID / OMCI over EPON

### 通用适配层（GEM / XGEM）

- [GEM/XGEM 封装、定界与碎片重组](gem-xgem/encapsulation-delineation.md) — XGEM 头逐字段、HEC 定界、分片/重组、空闲帧

## 阅读建议

先吃透 [GPON 激活状态机 ⭐](gpon-g984/activation-state-machine.md)，再看 XGS-PON 差异，效率最高 —— 两者 TC 层高度同源。
