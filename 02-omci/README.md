# OMCI 建模与白盒化

> OMCI（ONU Management and Control Interface，ITU-T G.988）是 PON 开发的重头戏。本模块用流程图 + ME 链路梳理常见业务（HSI / VoIP / IPTV）从 MIB Reset 到 Traffic Descriptor 的完整配置链路。

## 目录

- [OMCI 规范总览](omci-spec.md) — 消息格式（baseline/extended）、消息类型、操作、MIC、MIB 同步
- [MIB Upload 与 MIB 同步](mib-upload-sync.md) — MIB data sync、新/旧 ONU 上线、三种同步场景
- [软件升级（Software Image）](software-upgrade.md) — 双镜像、Download Section、Activate/Commit、失败回退
- [HSI 上网业务配置链路 ⭐](provisioning-hsi.md) — 从 MIB Reset 到 Traffic Descriptor 的完整 ME 下发链路
- [ME 速查表](me-reference.md) — 常见 Managed Entity 的 Class / 属性 / 创建者
- [VoIP 业务配置](provisioning-voip.md) — VoIP / SIP / POTS 相关 ME
- [IPTV 业务配置](provisioning-iptv.md) — 组播 ME（Multicast GEM IW TP / Operations Profile）

## 核心概念

- **ME（Managed Entity）**：OMCI 的建模单元，有 Class ID + Instance ID + 属性。
- **指针属性**：ME 之间通过指针（存放对端 Instance ID）串成业务链路。
- **AutoCreate vs OLT-Create**：部分 ME 由 ONU 自建（ONT-G/ANI-G/UNI-G），部分必须 OLT 显式 Create（T-CONT 绑定、GEM CTP、Bridge、TD）。

从 [HSI 上网业务配置链路 ⭐](provisioning-hsi.md) 入门最佳，它是其他业务的「主干」。
