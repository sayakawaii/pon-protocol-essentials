# Open Items（待办与差距分析）

> 对照原始需求，记录知识库尚缺的内容、优先级与迭代进度。完成一项就勾选并更新对应文档状态。

## 原始需求回顾

1. **动机**：把公有协议彻底内化，离职后仍可凭公有标准复核。
2. **协议栈深度梳理**：GPON (G.984.x)、XG-PON (G.987.x)、XGS-PON (G.9807.x)、EPON/10G-EPON 的**状态机、帧结构、PLOAM 消息、OMCI 规范**。
3. **OMCI 建模与白盒化**：HSI / VoIP / IPTV 的 ME 配置流程图（MIB Reset → Traffic Descriptor 完整链路）。
4. **DBA 算法原理**：T-CONT 类型、SR-DBA / NSR-DBA 核心调度逻辑。

## 已完成（旗舰，已写满）

- [x] [README](README.md) / [PON 全景](00-overview/pon-landscape.md) / [术语表](00-overview/glossary.md)
- [x] [GPON/XGS-PON 激活状态机 ⭐](01-protocol-stack/gpon-g984/activation-state-machine.md)
- [x] [OMCI HSI 配置链路 ⭐](02-omci/provisioning-hsi.md)
- [x] [T-CONT 类型 ⭐](03-dba/tcont-types.md) + [DBA 算法 ⭐](03-dba/dba-algorithms.md)
- [x] [PLOAM 消息列表](01-protocol-stack/gpon-g984/ploam-messages.md)（消息全集，缺字段级编码）
- [x] [代码索引映射](_sources/code-map.md)

## 差距分析与迭代计划

### 协议栈（需求 2）—— 缺口最大

| 项 | 当前状态 | 优先级 | 迭代 |
|----|----------|--------|------|
| GPON 帧结构（GTC 下行/上行帧、GEM 封装、BWmap、FEC） | ✅ 已写满 | 高 | 迭代 1 ✅ |
| XGS-PON 帧结构（XGTC/FS、PSBd、XGEM、RS(248,216)） | ✅ 已写满 | 高 | 迭代 2 ✅ |
| PLOAM 字段级编码（13B/48B 布局、MIC、SeqNum/Ack） | ✅ 已写满 | 高 | 迭代 3 ✅ |
| OMCI 规范总览（消息格式、操作、MIC、baseline/extended） | ✅ 已写满（[omci-spec.md](02-omci/omci-spec.md)） | 高 | 迭代 4 ✅ |
| XGS-PON 激活状态机（独立写满） | ✅ 已写满 | 中 | 迭代 5 ✅ |
| 测距与激活细节（EqD、静默窗、Drift/DOW/TIW） | 提纲 | 中 | 后续 |
| XG-PON vs XGS-PON 差异（逐条对照 G.987.3 ↔ Annex C） | 提纲 | 低 | 后续 |
| EPON / 10G-EPON（MPCP 发现/GATE/REPORT、OAM、LLID） | 提纲 | 低 | 后续 |

### OMCI 建模（需求 3）

| 项 | 当前状态 | 优先级 | 迭代 |
|----|----------|--------|------|
| ME 速查表（属性级：index/类型/长度/读写/SetByCreate） | ✅ 已写满 | 中 | 迭代 5 ✅ |
| VoIP 业务配置（SIP/POTS/VoIP Config Data 链路 + 流程图） | ✅ 已写满 | 中 | 迭代 5 ✅ |
| IPTV 业务配置（Multicast GEM IW TP/Operations Profile 链路 + 流程图） | ✅ 已写满 | 中 | 迭代 5 ✅ |
| OMCI MIB Upload / 告警 / PM（性能监控）章节 | 缺 | 低 | 后续 |

### DBA（需求 4）—— 基本完成，可增强

| 项 | 当前状态 | 优先级 |
|----|----------|--------|
| DBRu/SR 报告格式字段级（G.9807.1 C.8.1.2.2） | 缺 | 低 |
| TM-DBA（NSR）空闲帧观察法细节 | 概述 | 低 |
| DBA 性能/公平性参考模型与度量 | 缺 | 低 |

### 加分项（非原始需求，可选）

- [ ] 安全：PLOAM-IK / OMCI-IK 派生、AES-CTR 加密、Key Control/Report 流程
- [ ] 省电：Sleep/Doze/Watchful Sleep 状态与 PLOAM 交互
- [ ] 故障与告警：LOS/LOF/LODS/DG（Dying Gasp）等
- [ ] 互通测试要点（BBF TR-309 / TP-255 用例归纳）

## 迭代进度

- [x] 迭代 1：GPON 帧结构 — [frame-structure.md](01-protocol-stack/gpon-g984/frame-structure.md)
- [x] 迭代 2：XGS-PON 帧结构 — [frame-structure.md](01-protocol-stack/xgspon-g9807/frame-structure.md)
- [x] 迭代 3：PLOAM 字段级编码 — [GPON 13B](01-protocol-stack/gpon-g984/ploam-messages.md) + [XGS-PON 48B](01-protocol-stack/xgspon-g9807/ploam-messages.md)
- [x] 迭代 4：OMCI 规范总览 — [omci-spec.md](02-omci/omci-spec.md)
- [x] 迭代 5：OMCI 建模补全 + XGS-PON 状态机 — [ME 速查](02-omci/me-reference.md) / [VoIP](02-omci/provisioning-voip.md) / [IPTV](02-omci/provisioning-iptv.md) / [XGS-PON 状态机](01-protocol-stack/xgspon-g9807/activation-state-machine.md)
- [ ] 迭代 6（下一批）：测距与激活细节 / EPON MPCP 详解 / XG-PON 差异逐条 / 安全（密钥/加密）/ 省电 / 告警与 PM
