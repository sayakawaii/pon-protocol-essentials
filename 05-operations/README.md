# 05 · 运维与运行（Operations）

> ONU 在网运行期的两类机制：**省电（Power Management）** 与 **告警 / 性能监控（Alarms & PM）**。前者是 PLOAM 驱动的 TC 层状态机，后者由 OMCI 通知与 PM History Data ME 承载。

## 本节内容

| 文档 | 内容 |
|------|------|
| [ONU 省电 ⭐](power-management.md) | Doze / Cyclic Sleep / Watchful Sleep；4 态省电状态机；Sleep_Request / Sleep_Allow / FWI |
| [告警与性能监控](alarms-and-pm.md) | Alarm / AVC / Test Result；PM History Data ME + 15min 区间；TCA 门限越界；LOS/LOF/LODS/Dying Gasp |

## 与其他章节的关系

- [XGS-PON 激活状态机](../01-protocol-stack/xgspon-g9807/activation-state-machine.md)：LODS（O6）与省电状态机正交。
- [PLOAM 消息](../01-protocol-stack/xgspon-g9807/ploam-messages.md)：Sleep_Request / Sleep_Allow 的编码。
- [OMCI 规范](../02-omci/omci-spec.md)：Alarm / AVC / Test Result 通知机制。
- [安全](../04-security/key-management-encryption.md)：MIC error count PM 与密钥同步。
