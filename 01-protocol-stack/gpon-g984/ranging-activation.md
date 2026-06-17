# 测距与激活细节

> 测距（Ranging）是 PON 的关键机制：测出各 ONU 到 OLT 的往返时延，下发均衡时延（EqD），使所有 ONU 的上行突发在 OLT 接收端对齐、互不碰撞。本篇为提纲。

## 提纲（TODO）

- [ ] **为什么要测距**：P2MP 共享上行，不同 ONU 距离不同（往返时延不同），需对齐
- [ ] **静默窗（quiet window）**：发现新 ONU 时 OLT 暂停其他授权
- [ ] **EqD（Equalization Delay）**：计算与下发（Ranging_Time，octet 5 = 0x01 绝对）
- [ ] **测距精度与差分距离（Differential Distance）**：最大支持距离差
- [ ] **传输漂移（Drift）**：DOW（Drift of Window）、TIW（Transmission Interference Warning）
- [ ] 与 [激活状态机](activation-state-machine.md) O4 的衔接

## 来源（待检索补全）

- ITU-T G.984.3 §10（Ranging）、BBF TR-309 §8.4（Drift Control）、ITU-T G Supplement 46（时间参考）。
