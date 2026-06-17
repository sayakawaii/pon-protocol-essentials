# OMCI IPTV 业务配置链路

> IPTV（组播视频）业务的 OMCI 配置。核心是下行**组播**承载与 IGMP/MLD 监听（snooping）。本篇为提纲。

## 与 HSI 的关系

IPTV 在 [HSI 二层通路](provisioning-hsi.md) 之上，增加**组播专用 GEM 通道**与组播控制 ME。下行组播流通过 `Multicast GEM Interworking TP` 承载，与单播 GEM IW TP 并存于同一 MAC Bridge（参考 G.988 Figure II.1.3.1.1-1）。

## 提纲（TODO）

- [ ] **Multicast GEM Interworking TP**：承载下行组播流量
- [ ] **Multicast Operations Profile**：IGMP/MLD 版本、snooping/proxy 模式、组播 VLAN、ACL、带宽控制
- [ ] **Multicast Subscriber Config Info**：每用户的组播策略（最大同时组数、预览等）
- [ ] **Multicast Subscriber Monitor**：组播监控/统计
- [ ] 组播 VLAN 与单播 VLAN 的关系
- [ ] ME 链路流程图（含组播 MAC Bridge Port）

## 关键点

- 组播 GEM 通常下行单向；上行 IGMP 报文经单播通道回送。
- 「incidental broadcast」由独立的 GEM IW TP (null) 承载（G.988 L2-OCM 模型中的 yellow box）。

## 来源（待检索补全）

- ITU-T G.988 第 9.3.27~9.3.29 章（Multicast operations profile / subscriber config info / subscriber monitor）、Figure II.1.3.1.1-1（带组播的 L2-OCM）。
