# OMCI ME 速查表

> 常见 Managed Entity 的 Class / 关键属性 / 创建者速查。本篇为提纲，逐步补全属性级细节。已有的属性级实现可对照 `gopon/common/omci/me_g988.go`。

## 设备 / 接口级（ONU 自建）

| ME | Class | 关键属性 |
|----|-------|----------|
| ONT-G | — | vendor_id, version, serial_number, administrative_state, operational_state |
| ONT-2G | — | total_priority_queue_number, total_traffic_scheduler_number, total_gem_port_number, sys_up_time |
| ANI-G | — | total_tcont_number, sr_indication, piggyback_dba_reporting, optical_signal_level, sf/sd_threshold |
| UNI-G | — | administrative_state, management_capability |
| PPTP Ethernet UNI | — | (物理以太口) |
| ONU3-G | — | power_class, pon_interface_type, energy_consumed |

## 业务链路级（OLT Create / Set）

| ME | Class | 创建者 | 关键属性/指针 |
|----|-------|--------|---------------|
| T-CONT | 262 | Set | alloc_id, policy |
| GEM Port Network CTP | 268 | Create | port_id_value, tcont_pointer, td_pointer_us/ds, pq_pointer_ds |
| GEM Interworking TP | — | Create | interworking_option, 指向 GEM CTP + Mapper |
| 802.1p Mapper Service Profile | — | Create | 8 个 P-bit → GEM IW TP 指针 |
| MAC Bridge Service Profile | — | Create | (虚拟网桥) |
| MAC Bridge Port Config Data | — | Create | tp_type, tp_pointer（UNI 侧 / ANI 侧） |
| Traffic Descriptor | 280 | Create | CIR, PIR, 颜色标记 |
| VLAN Tagging Filter Data | — | Create | VLAN 过滤规则 |
| Extended VLAN Tagging Op | — | Create | VLAN 打标/剥标/转换 |
| Priority Queue | — | ONU 自建 | 上/下行队列权重 |

## 提纲（TODO）

- [ ] 每个 ME 的完整属性表（index、类型、长度、读写权限、SetByCreate）
- [ ] 属性掩码（AttrMask）编码规则
- [ ] ME 之间的指针关系全图（参考 G.988 Figure 9.3-1）

## 来源

- 工程实现：`gopon/common/omci/me_g988.go`（约 21 个 ME 的属性级注册）、`me_spec.go`。
- 公有标准：ITU-T G.988 第 9 章（ME 定义）。
