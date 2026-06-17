# XGS-PON 帧结构

> XGS-PON 的 XGTC / FS 帧与 XGEM 封装、PSBd 物理同步块。本篇为提纲。

## 提纲（TODO）

- [ ] **PSBd（G.987.3 §10）**：PSync（8B，固定 `0xC5E51840FD59BB49`）+ SFC（超帧计数器 51-bit + HEC）+ PON-ID + HEC
- [ ] **FS（Framing Sublayer）帧**：FS 头、BWmap 分区、PLOAM 分区、净荷
- [ ] **XGEM 封装**：XGEM 头、Port-ID、PLI、HEC、加密
- [ ] **FEC**：RS(248,216)（开销 ≈ 14.8%），与 DBA 字节预算的关系
- [ ] **BIP-8** 校验

## 工程实现参考

- `gopon/common/tc/psbd.go`：XGS-PON PSBd 24 字节布局（PSync + SFC + SFC HEC + PON-ID + PON-ID HEC + Pre-OAM），BIP-8。

## 来源（待检索补全）

- ITU-T G.9807.1 / G.987.3 §10（PSBd、FS 帧、XGEM）。
