# 25/50G-PON（Higher Speed PON, G.9804.x）

> 继 XGS-PON（10G）之后的下一代单/对称高速 PON：**25G-PON 与 50G-PON**，ITU-T 称 **HSP（Higher Speed PON）**，标准为 **G.9804 系列**。其 TC 层**继续沿用** XGS-PON / NG-PON2 的框架（同源），主要变化在 PMD（更高线速率、新 FEC）与若干 TC 增强（功率调平等）。本篇梳理要点与差异。

> 与 [XGS-PON 帧结构](../xgspon-g9807/frame-structure.md) / [PLOAM](../xgspon-g9807/ploam-messages.md) / [DBA](../../03-dba/dba-algorithms.md) 高度同源，建议先读 XGS-PON。

## 1. 标准家族（G.9804 系列）

| 标准 | 内容 |
|------|------|
| **G.9804.1** | HSP 通用要求（general requirements） |
| **G.9804.2** | HSP **公共 TC 层**（common TC layer，25G/50G 共用，承袭 G.9807.1/G.989.3） |
| **G.9804.3** | HSP **PMD 层**（25G、50G 的光接口、波长计划） |
| **25GS-PON MSA** | 产业联盟规范（25 Gigabit Symmetric PON），与 ITU 25G 对齐，落地更早 |

> 互通测试（[TR-309](../../06-interop/test-plan-overview.md)）把 **XG-PON / XGS-PON / 25GS-PON / HSP（G.9804.2）** 并列引用——正因 TC 层同源，用例可大量复用。

## 2. 速率与 FEC

| 代际 | 标准 | 下行 / 上行 | FEC |
|------|------|-------------|-----|
| GPON | G.984 | 2.5G / 1.25G | RS(255,239)（可选） |
| XG-PON | G.987 | 10G / 2.5G | RS(248,216) |
| XGS-PON | G.9807.1 | 10G / 10G | RS(248,216) |
| NG-PON2 | G.989 | n×10G（TWDM） | RS(248,216) |
| **25G-PON** | **G.9804** | **25G / 25G（或 25/10）** | **LDPC** |
| **50G-PON** | **G.9804** | **50G / 50G（或 50/25、50/10）** | **LDPC** |

- **FEC 换代**：HSP 在高线速率下改用 **LDPC（低密度奇偶校验）** 等更强的前向纠错，替代低速代的 RS 码，以补偿更高速率下劣化的链路预算。
- **非对称选项**：50G-PON 支持多种上/下行速率组合（50/50、50/25、50/10），按部署成本灵活选型。

## 3. TC 层：承袭 + 增强

HSP 公共 TC 层（G.9804.2）**结构上等同** XGS-PON/NG-PON2：

- **同**：PSBd、FS 帧/突发、XGEM 封装、48B PLOAM、DBA（SR/TM、DBRu/BWmap）、激活状态机 O1–O7、AES-CTR 安全。
- **增强 / 新增**：
  - **功率调平（Power levelling）**：`Acknowledgement` PLOAM 携带 **Attenuation** 与 **Power levelling capability**（G.9804.2 §11.2.6.7/§11.2.6.8）——OLT 指挥 ONU 调整发射功率，优化高速链路预算与突发动态范围。
  - **Burst profile** 扩展以适配更高速率与新 FEC（LDPC 码字参数）。
  - 漂移容限按速率细分（见 [互通测试](../../06-interop/test-plan-overview.md) 的 DOWi 分档：12.5/25/50G 各有 bit 阈值）。

## 4. 共存与演进

```mermaid
flowchart LR
    GPON["GPON 2.5G"] --> XGSPON["XGS-PON 10G"]
    XGSPON --> P25["25G-PON"]
    P25 --> P50["50G-PON"]
    XGSPON -. 同 ODN 波分共存 .- P25
    P25 -. 波分共存 .- P50
```

- HSP 设计为与既有 GPON/XGS-PON 在**同一 ODN 上波分共存**（通过共存元件 CEx + 新波长计划），支持运营商**平滑升级**而不重建光纤。
- 典型驱动：FTTR、企业专线、移动 5G/6G 前传与回传等高带宽场景。

## 5. 与 XGS-PON 的差异速记

| 维度 | XGS-PON | 25/50G-PON (HSP) |
|------|---------|------------------|
| 线速率 | 10G/10G | 25G、50G（含非对称） |
| FEC | RS(248,216) | LDPC（更强） |
| 功率调平 | 基础 | 增强（Attenuation + Power levelling capability） |
| TC 框架 | G.9807.1 Annex C | G.9804.2（同源承袭） |
| PMD | G.9807.1 Annex B | G.9804.3 |

## 来源

- **公有标准 / 规范**：
  - ITU-T **G.9804.1**（HSP 通用要求）、**G.9804.2**（HSP 公共 TC 层；§9 数据通道、§11.2.6.7 Attenuation、§11.2.6.8 Power levelling capability、§11 PLOAM、Table 11-28 SeqNo 等）、**G.9804.3**（HSP PMD）。
  - **25GS-PON MSA**（25 Gigabit Symmetric PON Specification, 2023）。
  - BBF **TR-309 Issue 3**（并列引用 G.987.3 / G.9807.1 / G.9804.2 / 25GS-PON；§5.5 PMD 参数；功率调平/突发 profile/漂移分档用例）。
- 说明：速率与 FEC（LDPC）对照含公有工程知识归纳；逐字段 TC/PMD 定义以 G.9804 系列原文为准。本篇按「高速代知识延伸」处理，重点落在与 XGS-PON 的差异。
