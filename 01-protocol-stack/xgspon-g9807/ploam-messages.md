# XGS-PON PLOAM 消息

> XGS-PON（G.9807.1 §C.11 / G.989.3 §11）的 48 字节 PLOAM 消息。消息语义与 GPON 大体对应；本篇为提纲。

> 下行/上行消息列表与角色见 [GPON PLOAM 消息](../gpon-g984/ploam-messages.md)（合并视图），XGS-PON 特有的波长调谐、System/Channel Profile 类消息在该表中已标注。

## XGS-PON 要点（TODO）

- [ ] 48 字节 PLOAM 布局：ONU-ID + Message type + SeqNo + Message content + MIC
- [ ] MIC：基于 PLOAM-IK 的完整性校验（CMAC）
- [ ] SeqNo / Acknowledge 重传（G.989.3 §11.3.5）
- [ ] 与 GPON 13B PLOAM 的逐字段差异

## 来源（待检索补全）

- ITU-T G.9807.1 §C.11、G.989.3 §11。
- 工程实现：`gopon/common/ploam/ids.go`、`ploam/frame.go`。
