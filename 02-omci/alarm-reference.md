# OMCI 告警速查表（按 ME）

> OMCI 告警（Alarm）由各 ME 的「Notifications」小节定义：每个 **Alarm number**（位）对应一种故障。本篇把常用 ME 的告警号集中成速查表，便于抓包/告警面板对照。依据 G.988 各 ME Notifications。

> 告警机制（Alarm/AVC/TCA）与 PM 见 [告警与性能监控](../05-operations/alarms-and-pm.md)；本篇是「告警号字典」。

## 1. 告警的承载

- ONU 用 **Alarm 通知**（消息）上报；每条携带一个**位图**，置位的位即当前告警的 Alarm number。
- 进入告警置位、离开告警清零；OLT 也可通过 **Get All Alarms** 同步当前告警状态。
- TCA（门限越界）是一类特殊 Alarm，号与 **Threshold Data 1** ME 的门限属性绑定（见 [告警与 PM](../05-operations/alarms-and-pm.md)）。

## 2. ANI-G（PON 侧接口，§9.2.1）—— 最关注

| Alarm # | 告警 | 含义 |
|---------|------|------|
| 0 | **Low received optical power** | 下行收光功率低于门限 |
| 1 | **High received optical power** | 下行收光功率高于门限 |
| 2 | **SF**（Signal Fail） | 基于 BER 的信号失败（误码超阈，较严重） |
| 3 | **SD**（Signal Degrade） | 信号劣化（误码升高，预警） |
| 4 | Low transmit optical power | 发光功率低 |
| 5 | High transmit optical power | 发光功率高 |
| 6 | Laser bias current | 激光器偏置电流越限（老化预警） |
| — | **Dying Gasp** | 掉电前告警（见 §5） |

> ANI-G 还提供 **Optical signal level**（2 补码，1 dBm 参考，0.002 dB 粒度）、**Lower/Upper optical threshold**（−127~0 dBm，0.5 dB 步进）等属性，用于读光功率与设门限。

## 3. ONU-G（设备整机，§9.1.1）

| Alarm # | 告警 | 含义 |
|---------|------|------|
| 0 | **Equipment alarm** | 内部接口功能性故障 |
| 1 | **Powering alarm** | 外部市电掉电（电池后备靠外部接口推断，AC 不再可维持充电） |
| 2 | **Battery missing** | 电池缺失 |
| 3 | Battery failure | 电池故障 |
| 4 | Battery low | 电池电量低 |
| 5 | Physical intrusion | 物理入侵（机箱被打开等） |
| 6 | ONU self-test failure | 自检失败 |
| 7 | Dying gasp | 掉电瞬间告警 |
| 8 | Temperature yellow / red | 温度预警/严重 |
| … | （详见 §9.1.1 表） | |

## 4. POTS / 语音相关（电压告警）

| Alarm # | 告警 | 含义 |
|---------|------|------|
| 10 | **Voltage yellow** | 线供电压低于推荐最小值，尚未停服，但可能限制（如同时摘机/振铃线数） |
| 11 | **Voltage red** | 已停掉部分业务以避免电源崩溃；受影响 PPTP 的运行状态指示 |

## 5. Dying Gasp（重点）

- **目的**：帮 OLT **区分「断纤」与「设备故障」**。
- **归属**：应由支撑 ANI 的 circuit pack **代为声明**——即 ONU 其他部分可能仍在运行，DG 只表征 **PON 光路 / 整机** 的状态。
- **冗余例外**：若 ONU 有冗余 PON 接口卡，**不应**用 DG 表示其中一个控制器的掉电/故障。
- 工程：区域停电会引发 **DG 风暴**，OLT 侧需聚合去抖（见 [告警与 PM](../05-operations/alarms-and-pm.md)）。

## 6. xDSL / PPTP 线路类告警（示例）

| Alarm # | 告警 | 含义 |
|---------|------|------|
| 0 | NE LOF | 近端帧失步 |
| 1 | NE LOS | 近端信号丢失 |
| 2 | NE LOL | 近端链路丢失 |
| 3 | NE LPR | 近端掉电 |
| 4 | Card alarm | 板卡告警 |
| 5–8 | FE LOF/LOS/LOL/LPR | 远端对应告警 |
| 9/10 | DRT up/down | 数据速率门限上/下调 |

## 7. 速查用法

1. 抓到告警位图 → 先看是哪个 **ME Class + Instance**；
2. 在该 ME 的 Notifications 表里查 **Alarm number** 对应含义（本表覆盖最常用者）；
3. 光层问题先看 **ANI-G**（收/发光、SF/SD）；供电/整机看 **ONU-G**；语音看电压告警；
4. 持续的 **MIC error**（PLOAM/OMCI）查密钥同步（见 [安全](../04-security/key-management-encryption.md)）。

## 来源

- **公有标准**：
  - ITU-T G.988 (2024) 各 ME 的 Notifications 小节：
    - §9.2.1 ANI-G（Alarm 0/1 收光低/高、2 SF、3 SD、收发光与激光偏流告警；Optical signal level、Lower/Upper optical threshold、Dying Gasp 归属说明）。
    - §9.1.1 ONU-G（0 Equipment、1 Powering、2 Battery missing、… Dying gasp、温度告警）。
    - POTS/语音 ME（10 Voltage yellow、11 Voltage red）。
    - xDSL/PPTP（NE/FE LOF/LOS/LOL/LPR、DRT up/down）。
- 说明：本表为常用告警号的归纳节选；每个 ME 的**完整且权威**的 Alarm number 列表以 G.988 该 ME 的 Notifications 表为准（不同 ME 的同一编号含义不同）。
