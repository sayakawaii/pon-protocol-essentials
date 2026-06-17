# OMCI 建模与白盒化

> OMCI（ONU Management and Control Interface，ITU-T G.988）是 PON 开发的重头戏。本模块用流程图 + ME 链路梳理常见业务（HSI / VoIP / IPTV）从 MIB Reset 到 Traffic Descriptor 的完整配置链路。

## 目录

- [OMCI 规范总览](omci-spec.md) — 消息格式（baseline/extended）、消息类型、操作、MIC、MIB 同步
- [消息格式：Baseline 与 Extended（逐字段）](message-formats.md) — 48B 基线 vs 变长扩展、Message Type 位结构
- [MIB Upload 与 MIB 同步](mib-upload-sync.md) — MIB data sync、新/旧 ONU 上线、三种同步场景
- [软件升级（Software Image）](software-upgrade.md) — 双镜像、Download Section、Activate/Commit、失败回退
- [HSI 上网业务配置链路 ⭐](provisioning-hsi.md) — 从 MIB Reset 到 Traffic Descriptor 的完整 ME 下发链路
- [ME 速查表](me-reference.md) — 常见 Managed Entity 的 Class / 属性 / 创建者
- [VoIP 业务配置](provisioning-voip.md) — VoIP / SIP / POTS 相关 ME
- [IPTV 业务配置](provisioning-iptv.md) — 组播 ME（Multicast GEM IW TP / Operations Profile）
- [VLAN 与 QoS 建模](vlan-qos-modeling.md) — Extended VLAN Tagging Op、DSCP→P-bit、802.1p Mapper
- [组播控制：IGMP / MLD](multicast-control.md) — snooping/proxy、IGMP 版本、ACL、Lost groups
- [告警速查表（按 ME）](alarm-reference.md) — ANI-G / ONU-G / 电压 / xDSL 告警号字典
- [远程诊断：Test / AVC](test-avc-operations.md) — Test/Test response/Test result、光线路监测、AVC 自主通知
- [消息逐字节示例（MIC 实例）](message-examples.md) — MT 位编码、Baseline/Extended 头、AES-CMAC MIC 计算实例
- [数据通道全景：L2 OMCI 配置模型 ⭐](datapath-l2-model.md) — UNI→GEM→T-CONT 端到端串讲
- [IP Host 与 TR-069 远程管理](ip-host-tr069.md) — IP 栈建模、ACS URL、OMCI vs TR-069 分工
- [TDM 业务与伪线（Pseudowire）](tdm-pseudowire.md) — CES/PW over Eth/UDP/MPLS、时钟恢复

## 核心概念

- **ME（Managed Entity）**：OMCI 的建模单元，有 Class ID + Instance ID + 属性。
- **指针属性**：ME 之间通过指针（存放对端 Instance ID）串成业务链路。
- **AutoCreate vs OLT-Create**：部分 ME 由 ONU 自建（ONT-G/ANI-G/UNI-G），部分必须 OLT 显式 Create（T-CONT 绑定、GEM CTP、Bridge、TD）。

从 [HSI 上网业务配置链路 ⭐](provisioning-hsi.md) 入门最佳，它是其他业务的「主干」。
