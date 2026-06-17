# 总索引（全文档地图）

> 全库文档一览，按模块分组。⭐ 为旗舰/核心章节。返回 [README](README.md)。

## 00 · 总览
- [PON 全景与标准对照](00-overview/pon-landscape.md) ⭐
- [术语表 Glossary](00-overview/glossary.md)

## 01 · 协议栈
### GPON (G.984.x)
- [激活状态机](01-protocol-stack/gpon-g984/activation-state-machine.md) ⭐
- [帧结构](01-protocol-stack/gpon-g984/frame-structure.md)
- [PLOAM 消息](01-protocol-stack/gpon-g984/ploam-messages.md)
- [测距与激活](01-protocol-stack/gpon-g984/ranging-activation.md)
### XGS-PON (G.9807.1)
- [激活状态机](01-protocol-stack/xgspon-g9807/activation-state-machine.md)
- [帧结构](01-protocol-stack/xgspon-g9807/frame-structure.md)
- [PLOAM 消息](01-protocol-stack/xgspon-g9807/ploam-messages.md)
### XG-PON (G.987.x)
- [与 XGS-PON 的差异](01-protocol-stack/xgpon-g987/delta-vs-xgspon.md)
### NG-PON2 (G.989.x)
- [TWDM 与波长调谐](01-protocol-stack/ngpon2-g989/overview.md)
### 25/50G-PON (G.9804.x, HSP)
- [概览](01-protocol-stack/hsp-g9804/overview.md)
- [功率调平与能力协商](01-protocol-stack/hsp-g9804/power-leveling.md)
### EPON / 10G-EPON (IEEE 802.3ah/av)
- [概览（对照）](01-protocol-stack/epon-10gepon/overview.md)
- [EPON OAM 与 OMCI over EPON](01-protocol-stack/epon-10gepon/oam-omci.md)
### 通用适配层 & 物理层
- [GEM/XGEM 封装、定界与碎片重组](01-protocol-stack/gem-xgem/encapsulation-delineation.md)
- [GPON vs XGS-PON 数值对照](01-protocol-stack/gpon-vs-xgspon.md)
- [前向纠错（FEC）原理与各代对照](01-protocol-stack/fec-principles.md)
- [光功率预算与光链路（ODN class）](01-protocol-stack/optical-power-budget.md)

## 02 · OMCI 建模
- [OMCI 协议规范概览](02-omci/omci-spec.md)
- [ME 参考速查](02-omci/me-reference.md)
- [消息格式（Baseline/Extended）](02-omci/message-formats.md)
- [消息逐字节示例（MIC 实例）](02-omci/message-examples.md)
- [MIB 上传与同步](02-omci/mib-upload-sync.md)
- [软件升级](02-omci/software-upgrade.md)
- [远程诊断：Test / AVC](02-omci/test-avc-operations.md)
- [告警速查表（按 ME）](02-omci/alarm-reference.md)
- [HSI 上网业务配置](02-omci/provisioning-hsi.md) ⭐
- [VoIP 语音业务配置](02-omci/provisioning-voip.md)
- [IPTV 业务配置](02-omci/provisioning-iptv.md)
- [VLAN 与 QoS 建模](02-omci/vlan-qos-modeling.md)
- [组播控制：IGMP / MLD](02-omci/multicast-control.md)
- [IP Host 与 TR-069 远程管理](02-omci/ip-host-tr069.md)
- [TDM 伪线（PW/CES）](02-omci/tdm-pseudowire.md)
- [数据通道全景 L2-OCM](02-omci/datapath-l2-model.md) ⭐
- [OMCI 白盒：代码走读](02-omci/code-walkthrough.md)
- [开通速查 Cookbook](02-omci/provisioning-cookbook.md)

## 03 · DBA 动态带宽分配
- [T-CONT 类型与带宽参数](03-dba/tcont-types.md) ⭐
- [DBA 算法原理](03-dba/dba-algorithms.md) ⭐
- [DBRu 与 BWmap 字段级格式](03-dba/dbru-bwmap-format.md)
- [DBA 参考模型与性能度量](03-dba/reference-model.md)
- [端到端 QoS 调度与带宽数值案例](03-dba/qos-scheduling-cases.md)
- [DBA 白盒：代码走读](03-dba/code-walkthrough.md)

## 04 · 安全
- [密钥层次与加密](04-security/key-management-encryption.md) ⭐
- [威胁模型与防护汇总](04-security/threat-model.md)

## 05 · 运维与运行
- [电源管理（Sleep/Doze/Watchful）](05-operations/power-management.md)
- [告警与性能监控](05-operations/alarms-and-pm.md)
- [流氓 ONU 检测与隔离](05-operations/rogue-onu.md)
- [PON 保护倒换（Type A/B/C）](05-operations/protection-switching.md)
- [时间与频率同步（ToD/1PPS/1588）](05-operations/time-synchronization.md)
- [SyncE 频率同步与时钟链](05-operations/synce-frequency.md)
- [排障实战：缺陷/抓包/决策树](05-operations/troubleshooting.md)

## 06 · 互通测试
- [互通测试要点（TR-309 / TP-255）](06-interop/test-plan-overview.md)
- [ONU 一致性与互联认证（BBF.247）](06-interop/onu-conformance.md)

## 附录
- [代码索引 code-map](_sources/code-map.md)
- [待办与迭代记录 open_items](open_items.md)
