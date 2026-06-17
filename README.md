# PON Protocol Essentials

> 一个面向 PON（Passive Optical Network）工程师的**纯知识库**：把公有协议与标准彻底内化，沉淀可离线查阅的协议栈、OMCI 建模、DBA 算法笔记。本仓库不含任何可运行代码。

## 为什么有这个仓库

PON 工程师的核心壁垒在**协议**与**业务逻辑**，而非某一家厂商的内部实现。一旦离开公司，内部 Wiki、需求文档、代码仓都会失去访问权限，但 ITU-T / IEEE / BBF 的**公有标准**是永远可得的。本仓库的目标是：

- 以公有标准（G.984.x / G.987.x / G.9807.1 / G.989.x / G.988 / IEEE 802.3）为骨架，系统化梳理协议栈、状态机、帧结构、PLOAM、OMCI、DBA；
- 用「标准 + 工程实现」双视角加深理解，把抽象条款映射到真实代码行为；
- 形成可检索、可演进的个人知识资产，离职后依然完整可用。

## 阅读导航

| 模块 | 目录 | 内容 |
|------|------|------|
| 总览 | [00-overview/](00-overview/) | PON 全景与标准对照、术语表 |
| 协议栈 | [01-protocol-stack/](01-protocol-stack/) | GPON / XGS-PON / XG-PON / EPON 状态机、帧结构、PLOAM |
| OMCI 建模 | [02-omci/](02-omci/) | ME 速查、HSI / VoIP / IPTV 业务配置链路 |
| DBA 算法 | [03-dba/](03-dba/) | T-CONT 类型、SR-DBA / NSR-DBA 调度逻辑 |
| 安全 | [04-security/](04-security/) | 密钥层次、AES-CTR 加密、MIC 完整性、双向认证 |
| 代码索引 | [_sources/code-map.md](_sources/code-map.md) | 标准条款 ↔ 工程实现的映射表 |

## 推荐学习路径

1. **建立坐标系**：先读 [00-overview/pon-landscape.md](00-overview/pon-landscape.md)，搞清 GPON / XG-PON / XGS-PON / NG-PON2 / EPON 的速率、波长、标准号关系。
2. **吃透激活流程**：[GPON 激活状态机](01-protocol-stack/gpon-g984/activation-state-machine.md) 是理解 PON「设备如何上线」的钥匙，O1→O5 全流程 + PLOAM 消息。
3. **打通业务链路**：[OMCI HSI 上网业务配置](02-omci/provisioning-hsi.md)，从 MIB Reset 到 Traffic Descriptor 的完整 ME 下发链路。
4. **理解带宽调度**：[T-CONT 类型](03-dba/tcont-types.md) + [DBA 算法](03-dba/dba-algorithms.md)，看 OLT 如何把上行带宽公平分给众多 ONU。

## 优先级与范围

- **重点**：GPON（ITU-T G.984.x）与 XGS-PON（ITU-T G.9807.1 / G.989.3）—— 贴合主流商用部署与实际工作。
- **差异化**：XG-PON（ITU-T G.987.x）以「与 XGS-PON 的差异」方式呈现。
- **对照**：EPON / 10G-EPON（IEEE 802.3ah / 802.3av）做简略对照，便于横向理解 IEEE 与 ITU 两条技术路线。

## 内容来源约定

每篇文档末尾标注三类来源，确保可追溯、可复核：

1. **公有标准**：通过 RAG 知识库（`iop-knowledge`）检索 ITU-T / BBF 原文条款；正文中以 `G.xxx 条款号` 形式引用。
2. **工程实现（Go）**：`gopon` 项目的 `common/` 库，作为「标准如何落地」的参考实现。
3. **工程实现（C++）**：`liteaggregator` 项目的 `src/glob/`，作为生产级 OLT 侧实现的参考。

> 术语、标准号、ME 名称、PLOAM 消息名一律保留英文原文；解释性叙述使用中文。

## 约定

- 所有内部链接使用**相对路径**，确保在 GitHub 上正常跳转。
- 图示优先使用 Mermaid（GitHub 原生渲染）；必要时辅以 `assets/` 下的图片。
- 标 ⭐ 的文档为已写满的「旗舰章节」；其余文档随学习推进逐步填充。
