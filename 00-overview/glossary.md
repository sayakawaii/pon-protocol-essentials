# 术语表 (Glossary)

> PON 领域高频缩略语速查。术语保留英文原文，附中文释义。完整缩略语清单见 ITU-T G.9807.1 / G.984.3 的 "Abbreviations and acronyms" 章节。

## 网元与拓扑

| 缩写 | 全称 | 释义 |
|------|------|------|
| OLT | Optical Line Terminal | 局端光线路终端，PON 的「主」 |
| ONU | Optical Network Unit | 用户侧光网络单元 |
| ONT | Optical Network Terminal | 单用户 ONU（家庭网关），常与 ONU 混用 |
| ODN | Optical Distribution Network | 光分配网（光纤 + 分光器） |
| UNI | User-Network Interface | 用户侧接口（如以太口、POTS 口） |
| SNI | Service Node Interface | 业务节点接口（OLT 上行侧） |
| PPTP | Physical Path Termination Point | 物理路径终结点（OMCI 中代表 UNI 物理口） |

## TC 层 / 帧 / 封装

| 缩写 | 全称 | 释义 |
|------|------|------|
| TC | Transmission Convergence | 传输汇聚层 |
| GTC | GPON Transmission Convergence | GPON 的 TC 层（G.984.3） |
| XGTC | 10G PON TC | XG(S)-PON 的 TC 层 |
| GEM | G-PON Encapsulation Method | GPON 封装方法 |
| XGEM | 10G GEM | XG(S)-PON 封装方法 |
| FS | Framing Sublayer | 成帧子层 |
| PSBd / PSBu | Physical Sync Block, downstream/upstream | 下行/上行物理同步块 |
| PSync | Physical Synchronization sequence | 物理同步序列 |
| SFC | Superframe Counter | 超帧计数器 |
| HEC | Hybrid Error Correction | 混合纠错（头部校验） |
| BIP | Bit-Interleaved Parity | 比特交织奇偶校验 |
| FEC | Forward Error Correction | 前向纠错（如 RS(248,216)） |
| PLI | Payload Length Indication | 净荷长度指示 |

## 标识符

| 缩写 | 全称 | 释义 |
|------|------|------|
| ONU-ID | ONU Identifier | 10-bit ONU 标识，经 Assign_ONU-ID 分配（0..1022，1023 广播） |
| Alloc-ID | Allocation Identifier | T-CONT 的带宽分配标识 |
| Port-ID | GEM Port Identifier | GEM/XGEM 端口标识，承载一条业务流 |
| SN | Serial Number | ONU 序列号（4B vendor + 4B VSSN） |
| VSSN | Vendor-Specific Serial Number | 厂商自定义序列号 |

## 信令与管理

| 缩写 | 全称 | 释义 |
|------|------|------|
| PLOAM | Physical Layer OAM | 物理层操作维护信令（48 字节消息） |
| PLOAMd / PLOAMu | PLOAM downstream / upstream | 下行/上行 PLOAM |
| OMCI | ONU Management and Control Interface | ONU 管理控制接口（G.988） |
| OMCC | ONU Management and Control Channel | 承载 OMCI 的专用 GEM Port 通道 |
| ME | Managed Entity | 管理实体（OMCI 的建模单元） |
| MIB | Management Information Base | 管理信息库（ONU 上所有 ME 实例集合） |
| MIC | Message Integrity Check | 消息完整性校验 |
| OAM | Operation, Administration and Maintenance | 操作维护管理（EPON 路线的管理协议） |

## DBA / QoS

| 缩写 | 全称 | 释义 |
|------|------|------|
| DBA | Dynamic Bandwidth Assignment | 动态带宽分配 |
| SR-DBA | Status Reporting DBA | 基于状态上报的 DBA |
| TM-DBA | Traffic Monitoring DBA | 基于流量监测的 DBA（即 NSR-DBA 的 OLT 侧观察法） |
| NSR | Non-Status-Reporting | 非状态上报（OLT 不依赖 ONU 显式缓存上报） |
| DBRu | Dynamic Bandwidth Report, upstream | 上行动态带宽报告（ONU 上报缓存深度） |
| BWmap | Bandwidth Map | 带宽映射表（OLT 下发的上行授权表） |
| T-CONT | Transmission Container | 传输容器，上行调度的基本单元 |
| CIR | Committed Information Rate | 承诺信息速率（保证带宽） |
| PIR | Peak Information Rate | 峰值信息速率（最大带宽） |
| BE | Best Effort | 尽力而为（服务类别） |
| NA | Non-Assured | 非保证（服务类别） |
| EqD | Equalization Delay | 均衡时延（测距结果） |

## ONU 状态（激活状态机）

| 状态 | 含义 |
|------|------|
| O1 | Initial — 初始（激光关闭，搜索下行同步） |
| O2 | Standby — 待机（已获下行同步） |
| O3 | Serial Number — 序列号上报（GPON），XGS-PON 合并为 O2-3 |
| O4 | Ranging — 测距 |
| O5 | Operation — 正常运行 |
| O6 | Intermittent LODS / Popup — 间歇性下行失步 |
| O7 | Emergency Stop — 紧急停止 |
| O8 / O9 | Downstream / Upstream Tuning — 波长调谐（NG-PON2 专有） |
| LODS | Loss of Downstream Synchronization | 下行同步丢失 |

详见 [GPON 激活状态机 ⭐](../01-protocol-stack/gpon-g984/activation-state-machine.md)。

## 来源

- 公有标准：ITU-T G.9807.1 (2023) 第 4 章 "Abbreviations and acronyms"；ITU-T G.984.3 / G.988 术语章节。
