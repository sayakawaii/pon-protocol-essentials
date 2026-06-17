# 代码索引映射（标准 ↔ 工程实现）

> 本页把 ITU-T 标准条款映射到两个参考实现的源码位置，方便在「读标准」与「看代码如何落地」之间快速切换。这些代码不在本仓库内，仅作学习参考。

## 参考实现

| 项目 | 语言 | 角色 | 路径 |
|------|------|------|------|
| `gopon` | Go | 教学/仿真栈：OLT(glob) + ONT 仿真 + vOMCI + dashboard，共享 `common/` 库 | `/home/mingheh/project/gopon` |
| `liteaggregator` | C++ | 生产级 OLT 侧实现 | `/home/mingheh/project/liteaggregator` |

## gopon/common 子包 ↔ 标准

| 子包 | 标准 | 知识库章节 |
|------|------|-----------|
| `types/` | G.984/987/989 标识符约定（OnuID、SerialNumber、OnuState O1-O9、PonTech） | [激活状态机](../01-protocol-stack/gpon-g984/activation-state-machine.md)、[术语表](../00-overview/glossary.md) |
| `ploam/` | G.984.3 §9.2 / G.989.3 §11 PLOAM 消息（DsMsgID / UsMsgID、Frame） | [PLOAM 消息](../01-protocol-stack/gpon-g984/ploam-messages.md) |
| `omci/` | G.988 ME 注册表（~21 个 ME：ONT-G、ANI-G、T-CONT、GEM Port CTP 等） | [OMCI HSI 配置](../02-omci/provisioning-hsi.md)、[ME 速查](../02-omci/me-reference.md) |
| `dba/` | DBA-lite 调度（TcontProfile、Schedule、CIR/PIR/PON-cap） | [DBA 算法](../03-dba/dba-algorithms.md) |
| `dba/swbwm/` | liteaggregator swbwm/modular_dba 内核移植（HRT/Assured/Non-Assured/BE、RS(248,216) FEC） | [DBA 算法](../03-dba/dba-algorithms.md)、[T-CONT 类型](../03-dba/tcont-types.md) |
| `tc/` | G.987.3 §10 PSBd、G.984.3 BIP-8、per-rate split | [GPON 帧结构](../01-protocol-stack/gpon-g984/frame-structure.md) |
| `gem/` | G.984.3 §8.1.1 GEM 头编码（5B 头、CRC-13 HEC、PTI、12-bit Port-ID/PLI） | [GPON 帧结构](../01-protocol-stack/gpon-g984/frame-structure.md) |
| `wire/` | 组合 gem + tc 的 canonical wire order | — |
| `bpselect/` | 按 PON tech × power class 选上行 burst profile | [DBA 算法](../03-dba/dba-algorithms.md)（FEC 预留） |
| `yang2omci/` | YANG → OMCI 翻译引擎 | [OMCI](../02-omci/README.md) |

## liteaggregator/src/glob 关键子目录

| 目录 | 角色 | 知识库章节 |
|------|------|-----------|
| `swbwm/` | 软件带宽管理（grant allocation 内核） | [DBA 算法](../03-dba/dba-algorithms.md) |
| `modular_dba/`、`unified_dba/`、`dba_xglob/`、`dba_common/` | 各代 DBA 调度内核 | [DBA 算法](../03-dba/dba-algorithms.md) |
| `models/` | OMCI ME 模型 | [OMCI HSI 配置](../02-omci/provisioning-hsi.md) |
| `ponMsg/` | PON 消息层（PLOAM/OMCI 收发） | [PLOAM 消息](../01-protocol-stack/gpon-g984/ploam-messages.md) |
| `bpselect/` | 突发 profile 选择 | — |

## 关键文件速查

| 主题 | 文件 |
|------|------|
| ONU 状态 O1-O9 | `gopon/common/types/types.go` |
| PLOAM 消息 ID | `gopon/common/ploam/ids.go` |
| OMCI ME 注册表 | `gopon/common/omci/me_g988.go` |
| OMCI ME 框架 | `gopon/common/omci/me_spec.go` |
| DBA-lite 调度算法 | `gopon/common/dba/scheduler.go` |
| 分级 DBA 内核 | `gopon/common/dba/swbwm/allocator.go` |
| TC 层 PSBd / BIP-8 | `gopon/common/tc/psbd.go` |
| common 库总览 | `gopon/common/README.md` |
