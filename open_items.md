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
| 测距与激活细节（EqD、静默窗、Drift/DOW/TIW） | ✅ 已写满 | 中 | 迭代 6 ✅ |
| 测距 EqD 计算逐步推导（C.13-6/7/8、精度、测距） | ✅ 已写满（[ranging §4](01-protocol-stack/gpon-g984/ranging-activation.md)） | 中 | 迭代 11 ✅ |
| XG-PON vs XGS-PON 差异（逐条对照 G.987.3 ↔ Annex C） | ✅ 已写满 | 低 | 迭代 6 ✅ |
| EPON / 10G-EPON（MPCP 发现/GATE/REPORT、OAM、LLID） | ✅ 已写满 | 低 | 迭代 6 ✅ |
| NG-PON2 TWDM（多波长架构、ONU 调谐、ICTP） | ✅ 已写满（[ngpon2 overview](01-protocol-stack/ngpon2-g989/overview.md)） | 低 | 迭代 8 ✅ |
| 25/50G-PON（G.9804.x / HSP：速率、LDPC、功率调平） | ✅ 已写满（[hsp overview](01-protocol-stack/hsp-g9804/overview.md)） | 低 | 迭代 9 ✅ |
| GEM/XGEM 定界与碎片重组（HEC 定界、分片、空闲帧） | ✅ 已写满（[gem-xgem](01-protocol-stack/gem-xgem/encapsulation-delineation.md)） | 低 | 迭代 10 ✅ |

### OMCI 建模（需求 3）

| 项 | 当前状态 | 优先级 | 迭代 |
|----|----------|--------|------|
| ME 速查表（属性级：index/类型/长度/读写/SetByCreate） | ✅ 已写满 | 中 | 迭代 5 ✅ |
| VoIP 业务配置（SIP/POTS/VoIP Config Data 链路 + 流程图） | ✅ 已写满 | 中 | 迭代 5 ✅ |
| IPTV 业务配置（Multicast GEM IW TP/Operations Profile 链路 + 流程图） | ✅ 已写满 | 中 | 迭代 5 ✅ |
| OMCI 告警 / PM（性能监控）章节 | ✅ 已写满（[05-operations/alarms-and-pm](05-operations/alarms-and-pm.md)） | 低 | 迭代 7 ✅ |
| OMCI MIB Upload 流程章节 | ✅ 已写满（[mib-upload-sync](02-omci/mib-upload-sync.md)） | 低 | 迭代 8 ✅ |
| OMCI 软件升级（Software Image）流程章节 | ✅ 已写满（[software-upgrade](02-omci/software-upgrade.md)） | 低 | 迭代 9 ✅ |
| OMCI baseline/extended 消息格式逐字段 | ✅ 已写满（[message-formats](02-omci/message-formats.md)） | 低 | 迭代 10 ✅ |
| VLAN 与 QoS 建模（Ext VLAN Tagging Op / 802.1p / DSCP→P） | ✅ 已写满（[vlan-qos-modeling](02-omci/vlan-qos-modeling.md)） | 中 | 迭代 11 ✅ |
| 组播 IGMP/MLD 控制（snooping/proxy/ACL/版本） | ✅ 已写满（[multicast-control](02-omci/multicast-control.md)） | 中 | 迭代 11 ✅ |
| 数据通道全景 L2-OCM 串讲（UNI→GEM→T-CONT） | ✅ 已写满（[datapath-l2-model](02-omci/datapath-l2-model.md)） | 中 | 迭代 12 ✅ |
| IP Host 与 TR-069 远程管理建模 | ✅ 已写满（[ip-host-tr069](02-omci/ip-host-tr069.md)） | 低 | 迭代 12 ✅ |
| TDM 业务与伪线（CES/PW） | ✅ 已写满（[tdm-pseudowire](02-omci/tdm-pseudowire.md)） | 低 | 迭代 12 ✅ |
| OMCI 告警速查表（按 ME 的 Alarm number） | ✅ 已写满（[alarm-reference](02-omci/alarm-reference.md)） | 低 | 迭代 13 ✅ |
| PON 保护倒换（Type A/B/C、双归属） | ✅ 已写满（[protection-switching](05-operations/protection-switching.md)） | 中 | 迭代 13 ✅ |
| GPON vs XGS-PON 数值/时序对照 | ✅ 已写满（[gpon-vs-xgspon](01-protocol-stack/gpon-vs-xgspon.md)） | 低 | 迭代 13 ✅ |

### DBA（需求 4）—— 基本完成，可增强

| 项 | 当前状态 | 优先级 |
|----|----------|--------|
| DBRu/SR 报告格式字段级（G.9807.1 C.8.1.2.2） | ✅ 已写满（[dbru-bwmap-format.md](03-dba/dbru-bwmap-format.md)） | 低 | 迭代 7 ✅ |
| TM-DBA（NSR）空闲帧观察法细节 | ✅ 已补（SR vs TM 对照） | 低 | 迭代 7 ✅ |
| DBA 性能/公平性参考模型与度量 | ✅ 已写满（[reference-model](03-dba/reference-model.md)） | 低 | 迭代 10 ✅ |

### 加分项（非原始需求，可选）

- [x] 安全：PLOAM-IK / OMCI-IK 派生、AES-CTR 加密、Key Control/Report 流程 — [04-security](04-security/key-management-encryption.md)（迭代 6 ✅）
- [x] 省电：Sleep/Doze/Watchful Sleep 状态与 PLOAM 交互 — [05-operations/power-management](05-operations/power-management.md)（迭代 7 ✅）
- [x] 故障与告警：LOS/LOF/LODS/DG（Dying Gasp）等 — [05-operations/alarms-and-pm](05-operations/alarms-and-pm.md)（迭代 7 ✅）
- [x] 互通测试要点（BBF TR-309 / TP-255 用例归纳） — [06-interop](06-interop/test-plan-overview.md)（迭代 8 ✅）
- [x] 流氓 ONU（Rogue ONU）检测与隔离 — [05-operations/rogue-onu](05-operations/rogue-onu.md)（迭代 9 ✅）

## 迭代进度

- [x] 迭代 1：GPON 帧结构 — [frame-structure.md](01-protocol-stack/gpon-g984/frame-structure.md)
- [x] 迭代 2：XGS-PON 帧结构 — [frame-structure.md](01-protocol-stack/xgspon-g9807/frame-structure.md)
- [x] 迭代 3：PLOAM 字段级编码 — [GPON 13B](01-protocol-stack/gpon-g984/ploam-messages.md) + [XGS-PON 48B](01-protocol-stack/xgspon-g9807/ploam-messages.md)
- [x] 迭代 4：OMCI 规范总览 — [omci-spec.md](02-omci/omci-spec.md)
- [x] 迭代 5：OMCI 建模补全 + XGS-PON 状态机 — [ME 速查](02-omci/me-reference.md) / [VoIP](02-omci/provisioning-voip.md) / [IPTV](02-omci/provisioning-iptv.md) / [XGS-PON 状态机](01-protocol-stack/xgspon-g9807/activation-state-machine.md)
- [x] 迭代 6：测距与激活细节 / EPON MPCP 详解 / XG-PON 差异逐条 / 安全章节 — [测距激活](01-protocol-stack/gpon-g984/ranging-activation.md) / [EPON](01-protocol-stack/epon-10gepon/overview.md) / [XG-PON 差异](01-protocol-stack/xgpon-g987/delta-vs-xgspon.md) / [安全](04-security/key-management-encryption.md)
- [x] 迭代 7：省电 / 告警与 PM / DBRu 字段级 — [省电](05-operations/power-management.md) / [告警与 PM](05-operations/alarms-and-pm.md) / [DBRu/BWmap](03-dba/dbru-bwmap-format.md)
- [x] 迭代 8：OMCI MIB Upload / NG-PON2 TWDM 调谐 / 互通测试要点 — [MIB 同步](02-omci/mib-upload-sync.md) / [NG-PON2](01-protocol-stack/ngpon2-g989/overview.md) / [互通测试](06-interop/test-plan-overview.md)
- [x] 迭代 9：OMCI 软件升级 / 25-50G-PON / 流氓 ONU — [软件升级](02-omci/software-upgrade.md) / [25-50G-PON](01-protocol-stack/hsp-g9804/overview.md) / [流氓 ONU](05-operations/rogue-onu.md)
- [x] 迭代 10：GEM/XGEM 定界与碎片 / OMCI 消息格式逐字段 / DBA 参考模型与度量 — [GEM/XGEM](01-protocol-stack/gem-xgem/encapsulation-delineation.md) / [消息格式](02-omci/message-formats.md) / [DBA 参考模型](03-dba/reference-model.md)
- [x] 迭代 11：VLAN/QoS 建模 / 组播 IGMP/MLD 控制 / 测距 EqD 推导 — [VLAN/QoS](02-omci/vlan-qos-modeling.md) / [组播控制](02-omci/multicast-control.md) / [EqD 推导](01-protocol-stack/gpon-g984/ranging-activation.md)
- [x] 迭代 12：数据通道全景 L2-OCM / IP Host 与 TR-069 / TDM 伪线 — [datapath 全景](02-omci/datapath-l2-model.md) / [IP Host/TR-069](02-omci/ip-host-tr069.md) / [TDM 伪线](02-omci/tdm-pseudowire.md)
- [x] 迭代 13：保护倒换 Type A/B/C / OMCI 告警速查表 / GPON vs XGS-PON 数值对照 — [保护倒换](05-operations/protection-switching.md) / [告警速查](02-omci/alarm-reference.md) / [数值对照](01-protocol-stack/gpon-vs-xgspon.md)
- [ ] 迭代 14（下一批）：ONU 一致性测试（OD-247/TP-247） / OMCI Test/AVC 操作细节 / G.984 GEM vs XGEM 迁移要点 / 时间同步(ToD/1PPS)分发
