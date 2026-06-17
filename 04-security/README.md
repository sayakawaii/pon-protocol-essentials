# 04 · 安全（Security）

> XGS-PON / XG-PON 的安全机制：密钥层次、XGEM 载荷加密（AES-CTR）、PLOAM/OMCI 完整性保护（MIC），以及可选的双向认证。

PON 是共享介质：**下行帧对所有 ONU 广播**。任何 ONU 在物理上都能收到发往别家的下行 XGEM 帧，因此**下行用户数据必须加密**，且密钥按 ONU 隔离。上行因点到点，威胁较小，但管理消息（PLOAM/OMCI）需**完整性保护**防篡改。

## 本节内容

| 文档 | 内容 |
|------|------|
| [密钥层次与加密 ⭐](key-management-encryption.md) | MSK → SK → 各派生密钥；AES-CTR 载荷加密；MIC 完整性；密钥协商（Key Control/Report）；双向认证 |
| [威胁模型与防护汇总](threat-model.md) | 窃听/冒充/盗用/DoS 威胁；防护对照；流氓 ONU；应急状态快照；安全检查清单 |

## 与其他章节的关系

- [XGS-PON 帧结构](../01-protocol-stack/xgspon-g9807/frame-structure.md)：XGEM 头的 Key-Index、加密发生在 FS 载荷层。
- [XGS-PON PLOAM 消息](../01-protocol-stack/xgspon-g9807/ploam-messages.md)：PLOAM 48B 帧尾的 8B MIC、Key 相关 PLOAM。
- [OMCI 规范](../02-omci/omci-spec.md)：OMCI 帧的 MIC 由 OMCI_IK 保护。
