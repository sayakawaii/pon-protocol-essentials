# GPON vs XGS-PON：参数与时序数值对照

> 把 GPON（G.984.x）与 XGS-PON（G.9807.1）的**关键数值**并排，便于换算与记忆：线速率、帧长、同步块、FEC、封装头、授权粒度等。两者**帧周期都是 125 µs**，但帧内字节数、FEC、头格式不同。

> 详细帧结构见 [GPON 帧结构](gpon-g984/frame-structure.md) / [XGS-PON 帧结构](xgspon-g9807/frame-structure.md)；XG-PON 差异见 [XG-PON vs XGS-PON](xgpon-g987/delta-vs-xgspon.md)。

## 1. 总览数值表

| 维度 | GPON (G.984) | XGS-PON (G.9807.1) |
|------|--------------|--------------------|
| 下行线速率 | 2.48832 Gbit/s | 9.95328 Gbit/s |
| 上行线速率 | 1.24416 Gbit/s | 9.95328 Gbit/s（对称） |
| **帧周期** | **125 µs** | **125 µs** |
| 下行帧字节数 | 38 880 B（@2.48832G） | **155 520 B（38 880 words）**（@9.95328G） |
| 上行帧/突发 | 突发结构，125µs 周期 | 155 520 B / 125 µs |
| 物理同步 | PCBd（含 Psync 0xB6AB31E0…） | **PSBd 24 B**（PSync 64-bit 0xC5E51840FD59BB49） |
| 超帧计数 | SFC（FrameCounter） | **SFC 51-bit**（PSBd 内） |
| **FEC** | RS(255,239)（可选） | **RS(248,216)**（强制下行） |
| FEC 码字/帧 | — | **627 个**（@9.95328G，216 数据+32 校验） |
| 封装 | **GEM**，5 字节头 | **XGEM**，8 字节头 |
| 封装头字段 | PLI 12b / Port-ID 12b / PTI 3b / HEC 13b | PLI 14b / KI 2b / Port-ID 16b / Options 18b / LF 1b / HEC 13b |
| PLOAM | **13 字节** | **48 字节**（含 8B MIC） |
| 加密 | AES（GPON 演进） | **AES-128-CTR**（XGEM 载荷） |
| 授权粒度 | 字节级（GTC） | **word(4B)@2.5G / block(16B)@10G** 上行 |

## 2. 帧周期与字节数的换算

帧周期固定 **125 µs**（与 PDH 8kHz 同源，便于承载 TDM）：

```
帧字节数 = 线速率 × 125µs / 8
GPON 下行 : 2.48832e9 × 125e-6 / 8 = 38 880 B
XGS 下行  : 9.95328e9 × 125e-6 / 8 = 155 520 B  (= 38 880 words ×4)
```

> 记忆点：XGS-PON 正好是 GPON 下行速率的 **4 倍**（2.48832→9.95328），所以字节数也是 4 倍（38880 B → 38880 words = 155520 B）。

## 3. XGS-PON 帧内结构（G.9807.1 §C.10）

| 项 | 值 |
|----|-----|
| 下行 PHY 帧 | 155 520 B = PSBd(24 B) + 载荷(155 496 B) |
| 下行载荷来源 | 下行 FS 帧 → 加 FEC → 加扰 |
| FEC 码字 | RS(248,216)，216 数据 + 32 校验；PSBd **不**入 FEC |
| 第一码字起点 | PHY 帧第 **25** 字节（FS header 首字节）；其后每 248 B 一个 |
| GrantSize | 16-bit，= FS 载荷 + DBRu；**不含** FS 头/尾/FEC 开销 |
| 授权粒度 | 2.48832G 上行：1 word(4B)；9.95328G 上行：1 block(16B) |

## 4. 同步与计数

| 项 | GPON | XGS-PON |
|----|------|---------|
| 物理同步块 | PCBd（Psync + Ident + PLOAMd + BWmap …） | PSBd（PSync 8B + SFC 8B + OC 结构），固定 24 B |
| PSync 模式 | 4 字节 0xB6AB31E0 | 8 字节 0xC5E51840FD59BB49 |
| 超帧计数 | Ident 中 FrameCounter | SFC 51-bit |
| 加密计数器 | — | **初始计数器块 = SFC + IFC**（C.15.4.3，构造时省去 SFC 的 MSB） |

## 5. 为什么 125 µs 不变

- **8 kHz 帧率**与传统 TDM（E1/T1、SDH）一致，便于做 [TDM 伪线](../02-omci/tdm-pseudowire.md) 与时间同步；
- 不同代际只是**往同样的 125 µs 里塞更多字节**（提速），帧结构思想一脉相承——这是 XGS-PON「继承 GPON」的根本体现。

## 6. 速记口诀

- **125 µs 恒定**，速率翻倍则帧字节翻倍；
- GPON：**5B GEM 头 / 13B PLOAM / RS(255,239)**；
- XGS-PON：**8B XGEM 头 / 48B PLOAM / RS(248,216) / PSBd 24B**；
- 上行授权粒度：2.5G 看 **word(4B)**，10G 看 **block(16B)**。

## 来源

- **公有标准**：
  - ITU-T G.9807.1 (2023) §C.10.1.1（下行 PHY 帧 125µs=155520B=38880 words，PSBd 24B+载荷）、§C.10.1.1.4（下行载荷 155496B）、§C.10.1.2（上行 PHY 帧 125µs/155520B）、§C.10.1.3.1.1（下行 FEC RS(248,216)、627 码字、首码字起于第 25 字节）、§C.8.1.1.2.4（GrantSize 16-bit，不含 FS 头/尾/FEC；粒度 word(4B)@2.5G / block(16B)@10G）、§C.15.4.3（初始计数器块=SFC+IFC，省 SFC 的 MSB）。
  - ITU-T G.984.3（GPON：2.48832/1.24416 Gbit/s、125µs 帧、PCBd/Psync、GEM 5B 头、PLOAM 13B、RS(255,239)）。
- 说明：换算公式为标准数值推导；逐字段以各自帧结构章节与原文为准。
