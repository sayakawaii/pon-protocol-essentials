# DBA 动态带宽分配

> 整理各类 T-CONT 类型及 SR-DBA / NSR-DBA 的核心调度逻辑。标准只规定信令与接口（SR 报告、BWmap 格式），调度算法本身是厂商差异化的核心壁垒。

## 目录

- [T-CONT 类型与带宽参数 ⭐](tcont-types.md) — 5 种 T-CONT、Fixed/Assured/Non-Assured/Best-Effort、Traffic Descriptor
- [DBA 算法原理 ⭐](dba-algorithms.md) — SR-DBA vs NSR-DBA、DBRu/BWmap 闭环、分级调度内核
- [DBRu 与 BWmap 字段级格式](dbru-bwmap-format.md) — DBRu 4B 结构、BufOcc 计量、BWmap allocation 字段、SR vs TM
- [DBA 参考模型与性能度量](reference-model.md) — Traffic Descriptor 六元组、带宽分量、稳态/恢复/收敛指标
- [端到端 QoS 调度与带宽分配数值案例](qos-scheduling-cases.md) — 三级调度模型、严格优先/WRR、TR-156 算例、DBA 分配算例
- [DBA 白盒：代码走读](code-walkthrough.md) — gopon Schedule/swbwm Allocator + liteaggregator C++ 内核逐行对标

## 一句话理解

OLT 通过 DBA 把共享的上行 TDMA 带宽，按各 T-CONT 的契约（CIR/PIR/优先级/权重）和实时需求（DBRu/SR 上报），公平且高效地分配下去（BWmap 授权）。

建议先读 [T-CONT 类型 ⭐](tcont-types.md) 建立「调度单元」概念，再读 [DBA 算法 ⭐](dba-algorithms.md) 理解调度逻辑。
