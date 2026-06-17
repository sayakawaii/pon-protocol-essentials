# OMCI VoIP 业务配置链路

> VoIP（语音）业务的 OMCI 配置。在 HSI 数据通路基础上，增加语音相关 ME。本篇为提纲。

## 与 HSI 的关系

VoIP 复用 [HSI 配置链路](provisioning-hsi.md) 的二层通路（GEM → T-CONT），通常用**独立的 GEM Port + T-CONT**（高优先级，常为 Fixed/Assured T-CONT）承载信令与媒体流，再叠加语音业务 ME。

## 提纲（TODO）

- [ ] **VoIP Config Data / VoIP Voice CTP**：语音承载配置
- [ ] **SIP Agent Config Data / SIP User Data**：SIP 协议栈配置（proxy、registrar、用户账号）
- [ ] **MGC Config Data**：H.248/MGCP 网关控制（如适用）
- [ ] **POTS UNI**：模拟话机口（PPTP POTS UNI）
- [ ] **VoIP Media Profile / Voice Service Profile**：编解码（G.711/G.729）、抖动缓冲
- [ ] **RTP / Network Dial Plan Table**
- [ ] T-CONT 选型：VoIP 信令/媒体宜用 Fixed (Type 1) 或 Assured (Type 2)
- [ ] ME 链路流程图（从 MIB 已有通路到 POTS UNI 解锁）

## 来源（待检索补全）

- ITU-T G.988 第 9.9 章（VoIP/语音相关 ME）。
