# XGS-PON ONU 激活状态机

> XGS-PON（G.9807.1 Annex C）的激活状态机与 GPON 高度同源。本篇聚焦 XGS-PON 特有点；完整状态机叙述见 GPON 版旗舰章节。

> 完整的 O1-O7 状态语义、时序、定时器、输入事件已在 [GPON/XGS-PON 激活状态机 ⭐](../gpon-g984/activation-state-machine.md) 中以 G.9807.1 Annex C.12 为主要依据写满。本页只标注 XGS-PON 视角的要点。

## XGS-PON 要点

- 状态命名：**O2-3 合并**（GPON 的 O2 Standby + O3 Serial Number → XGS-PON 的 O2-3 Serial Number state）。
- 子态：`O1/Off-Sync`、`O1/Profile Learning`、`O5/Associated`。
- PLOAM：48 字节，带 PLOAM-IK MIC。
- 定时器：TO1（O4 ranging）、TO2（O6 LODS）。

## 提纲（TODO）

- [ ] FS 帧同步状态机（C.10.1.1.3）：Hunt → Pre-Sync → Sync → Re-Sync
- [ ] 25GS-PON / G.9804.2 的状态机继承关系
- [ ] 与 NG-PON2 O8/O9 波长调谐的关系

## 来源

- ITU-T G.9807.1 (2023) Annex C.12.1.4（已在 GPON 版旗舰章节详引）。
