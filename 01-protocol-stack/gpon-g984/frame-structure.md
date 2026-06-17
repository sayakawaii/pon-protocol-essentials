# GPON 帧结构

> GPON GTC 层的下行帧、上行突发帧与 GEM 封装。本篇为提纲，内容随学习推进填充。

## 提纲（TODO）

- [ ] **GTC 下行帧（G.984.3 §8）**：PCBd（Physical Control Block downstream）= PSync + Ident + PLOAMd + BIP + Plend + US BWmap；净荷区
- [ ] **上行突发帧**：PLOu（前导/定界/BIP/ONU-ID/Ind）+ PLOAMu + PLSu + DBRu + 净荷
- [ ] **BWmap 结构**：Access 字段（Alloc-ID、Flags(DBRu/PLOAMu)、StartTime、StopTime）
- [ ] **GEM 封装（G.984.3 §8.1.1）**：5 字节 GEM 头（PLI 12-bit、Port-ID 12-bit、PTI 3-bit、HEC CRC-13）、分片
- [ ] **FEC**：RS(255,239)，开销与 BWmap 关系
- [ ] 与 [DBA](../../03-dba/dba-algorithms.md) 的 BWmap 衔接

## 工程实现参考

- GEM 头编码：`gopon/common/gem/frame.go`（5B 头、CRC-13 HEC、PTI、12-bit Port-ID/PLI）
- TC 层 PSBd / BIP-8：`gopon/common/tc/psbd.go`

## 来源（待检索补全）

- ITU-T G.984.3 §8（GTC 帧结构、GEM 封装）。
