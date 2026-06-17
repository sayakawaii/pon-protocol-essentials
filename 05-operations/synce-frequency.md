# SyncE 频率同步与 PON 时钟链

> 上篇 [时间同步](time-synchronization.md) 讲**相位/时间（ToD）**；本篇讲**频率同步**——让 ONU 输出口的时钟频率可溯源到网络参考时钟（PRC）。XGS-PON 通过「OLT 锁频 → PON 线速率携带频率 → ONU 恢复」实现频率分发，并用 **ESMC/SSM** 传递时钟质量等级。依据 G.9807.1 附录 A.I/A.V、G.988 §9.12.21（SyncE ME）、ITU-T G.8262/G.8264。

## 1. 频率 vs 时间（别混）

| 维度 | 同步对象 | PON 手段 | 本篇/上篇 |
|------|----------|----------|-----------|
| **频率（SyncE）** | 时钟**快慢**一致（ppb 级） | 线速率锁频 + ESMC | 本篇 |
| **时间/相位（ToD）** | 绝对**时刻**一致（µs 级） | SFC 时间锚 + EqD 补偿 | [时间同步](time-synchronization.md) |

> 5G TDD 既要频率也要时间；FDD 通常只要频率。

## 2. PON 频率分发链（A.I.10）

```mermaid
flowchart LR
    PRC["网络参考时钟 PRC"] --> BITS["BITS / SyncE / 1588"]
    BITS --> OLT["OLT (master for PON timing)"]
    OLT -->|"下行 PON 线速率锁频"| ONU["ONU 从下行码流恢复频率"]
    ONU --> OUT["ONU UNI 输出 (SyncE/时钟)"]
```

- **OLT 必须取得高质量、可溯源的时钟**，作为整条 PON 接口定时的 master；
- **正常源 = BITS**（Building Integrated Timing Source）定时输入；
- BITS 不可用时的替代：来自 SNI 的**同步线路定时**（可溯源到网络时钟），或**分组定时**（packet-based / 1588）。
- ONU 从**下行连续码流**恢复频率（下行 PON 线速率已锁到网络频率）。

## 3. OLT 锁频的几种来源（附录 A.V 用例）

| 用例 | OLT 网络侧输入 | 下行 PON 线速率锁到 |
|------|----------------|---------------------|
| 5 | **Synchronous Ethernet** | SyncE 线速率 |
| 6 | 非同步以太 | **IEEE 1588** ToD 导出频率 |
| 4 | 无 SyncE | 从 **IEEE 1588** 导出（ESMC QL 对应 1588 时钟质量） |

- 网络接口为以太时，线速率**可能同步**于网络频率参考（SyncE）**或不同步**；
- OLT 用 SyncE / 1588 / 其他物理层同步接口锁频，并用 [§C.13](time-synchronization.md) 方法把 ToD 传给 ONU。

## 4. ESMC / SSM —— 传递时钟质量

**ESMC**（Ethernet Synchronization Messaging Channel）特性：

- **简单、无状态、单向**协议，传递当前参考时钟的**质量等级 QL**；
- 走 IEEE 802.3 **OSSP**（Organization Specific Slow Protocol），目的地址为 Slow Protocol 组播地址；
- 只有一种消息类型：**SSM**（Synchronization Status Message）；
- **约 1 msg/秒**，携带 **QL（Quality Level）**。

> QL 让下游节点知道上游时钟**有多好**，据此选源/进入 holdover、避免**定时环路**。PON 上 ESMC 必须由 **OLT/ONU 作为一个系统**协同处理（透传/重生成 SSM）。

## 5. OMCI 建模：Synchronous Ethernet operation ME（§9.12.21）

- 作用：配置 ONU 监控/控制 **SyncE 能力**（依 ITU-T G.8264）；
- **自动创建/删除**（支持 SyncE 的 ONU）；与 **ONU-G** 关联，**仅一个实例（#0）**；
- 关键属性：**Administrative state**（lock=1 / unlock=0）等，OLT 借此使能/锁定 SyncE。

## 6. 工程检查清单

1. OLT 锁频源在线且可溯源（BITS / SyncE / 1588），无定时环路；
2. ONU 下行频率恢复正常（无频偏告警）；
3. ESMC/SSM 的 QL 端到端透传正确，下游按 QL 选源；
4. ONU 的 Synchronous Ethernet operation ME 已使能（unlock）；
5. 需要时间同步的场景叠加 [ToD 分发](time-synchronization.md)（频率是时间的前提）。

## 来源

- **公有标准**：
  - ITU-T G.9807.1 (2023) 附录 A.I（Fig A.I.10 网络时钟方案：OLT 取高质量可溯源时钟作 PON master、正常源 BITS、替代为 SNI 同步线路定时或分组定时；ONU 从下行码流恢复频率）、附录 A.V（用例 4/5/6：OLT 锁频于 SyncE 或 1588；网络以太可同步/不同步；ToD 用 §C.13 方法）、ESMC 特性（无状态单向、OSSP/Slow Protocol 组播、单一 SSM、~1 msg/s 携带 QL；PON 上 OLT/ONU 作为系统处理 ESMC）。
  - ITU-T G.988 (2024) §9.12.21 Synchronous Ethernet operation ME（依 G.8264；自动创建、关联 ONU-G、单实例 #0；Administrative state lock/unlock）。
  - 参考：ITU-T G.8262（EEC 时钟）、G.8264（ESMC/SSM）。
- 说明：频率/时间区分与检查清单为工程归纳；用例与 ME 属性以原文为准。
