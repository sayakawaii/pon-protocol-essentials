# DBA 白盒：代码走读（gopon Go + liteaggregator C++）

> 把 [DBA 算法](dba-algorithms.md) 的「调度内核」落到**真实代码**：gopon 的 `Schedule`（DBA-lite）与 `swbwm.Allocator`（liteaggregator 内核的 Go 移植），以及 liteaggregator C++ 的 `grant_bucket_accumulation` / `fec`。读完能把标准术语逐行对上实现。

> 代码索引见 [code-map](../_sources/code-map.md)；标准侧见 [DBA 算法](dba-algorithms.md) / [DBRu/BWmap](dbru-bwmap-format.md) / [QoS 案例](qos-scheduling-cases.md)。

## 1. 调度单元的数据结构 ↔ G.988

gopon 用 `TcontProfile` 表达每个 T-CONT 的带宽契约，字段直接对应 G.988 T-CONT(类 262) + Traffic Descriptor(类 280)：

```18:25:gopon/common/dba/scheduler.go
type TcontProfile struct {
	AllocID  uint16
	Policy   uint8 // 0=null/strict-priority, 1=hard-real-time, 2=WRR
	Weight   uint8 // populated when policy=WRR; defaults to 1
	CirBps   uint64
	PirBps   uint64
	Reason   string // free-form trace ("vendor override applied", ...)
}
```

- `AllocID` ↔ Alloc-ID（[T-CONT 类型](tcont-types.md)）；`CirBps/PirBps` ↔ Traffic Descriptor 的 CIR/PIR（bps，与 G.988 线格式一致）；`Policy` ↔ T-CONT policy 八位组（strict / WRR）。
- 注释点出工程要点：**缺失 PIR 当硬零处理**（不给 grant），避免错配的 T-CONT 蹭带宽。

## 2. DBA-lite：一眼看懂的端到端环（gopon `Schedule`）

`Schedule` 是一个「看得见的最小闭环」，三步走：

1. **CIR 保底**：`cir_bps × cycle_len ÷ 8` 换成每周期字节，**无论 SR 有无需求都先给**（GPON 每个 CIR T-CONT 也有空闲帧机会）。
2. **算超额需求**：`SR.BytesPending − CIR`，下限 0。
3. **按权重降序发**：strict-priority 隐式权重 255，WRR 按权重，受 **PIR 上限**与 **PON 每周期字节预算**封顶。

bps↔字节换算与 PON 预算封顶：

```78:87:gopon/common/dba/scheduler.go
	cycleSecs := in.CycleLen.Seconds()
	// bps -> bytes/cycle
	bpsToBytes := func(bps uint64) uint64 {
		return uint64(float64(bps) / 8.0 * cycleSecs)
	}
	ponBudget := bpsToBytes(ponCapacityBps)
	if ponCapacityBps == 0 {
		// effectively unlimited
		ponBudget = ^uint64(0)
	}
```

CIR 保底 + 超额到 PIR 的核心逻辑：

```105:131:gopon/common/dba/scheduler.go
		// Guaranteed CIR slice.
		grant := cir
		reason := fmt.Sprintf("CIR %d bps -> %d B", p.CirBps, cir)

		// Remaining demand above CIR.
		remaining := demand[p.AllocID]
		if remaining > cir {
			extra := remaining - cir
			// PIR ceiling.
			if grant+extra > pir {
				extra = pir - grant
				reason = fmt.Sprintf("ceiling=PIR %d bps -> %d B (demand=%d B)", p.PirBps, pir, remaining)
			} else {
				reason = fmt.Sprintf("CIR %d + best-effort %d = %d B (demand=%d B)", cir, extra, grant+extra, remaining)
			}
			grant += extra
		} else if remaining > 0 {
			reason = fmt.Sprintf("SR within CIR (demand=%d B, CIR=%d B)", remaining, cir)
		} else if cir == 0 {
			reason = "no demand, no CIR -> 0 B"
		}
```

优先级排序（strict-priority 永远在队头）：

```157:176:gopon/common/dba/scheduler.go
func effectiveWeight(p TcontProfile) int {
	switch p.Policy {
	case 0:
		// strict priority: always first
		return 255 + int(p.Weight)
	case 2:
		w := int(p.Weight)
		if w == 0 {
			w = 1
		}
		return w
	default:
		// "hard real time" and anything else: between SP and WRR.
		w := int(p.Weight)
		if w == 0 {
			w = 1
		}
		return 100 + w
	}
}
```

> 这正是标准里「先保 Fixed/Assured，再按优先级/权重分剩余」的最小可运行版（对照 [QoS 案例](qos-scheduling-cases.md) §4）。

## 3. 生产级内核：swbwm Allocator（liteaggregator 移植）

`swbwm.Allocator` 把 DBA-lite 升级成贴近真实 OLT 的内核，新增四要素：

### 3.1 四个策略类（policy_level）

```138:160:gopon/common/dba/swbwm/allocator.go
const (
	ClassHardRealTime PolicyClass = iota // policy=0 in OMCI (strict priority)
	ClassAssured                         // policy=1 (CIR-only)
	ClassNonAssured                      // PIR ceiling, no CIR (between 1 and 2)
	ClassBestEffort                      // policy=2 + WRR
)

// classOf maps an OMCI T-CONT policy octet (clause 9.2.3) to a
// PolicyClass. liteaggregator's `priority_level` field is the same
// concept but reads from a vendor-specific provisioning table.
func classOf(p dba.TcontProfile) PolicyClass {
	switch p.Policy {
	case 0:
		return ClassHardRealTime
	case 1:
		if p.CirBps == 0 {
			return ClassNonAssured
		}
		return ClassAssured
	default:
		return ClassBestEffort
	}
}
```

- 直接对应 [T-CONT 类型](tcont-types.md) 的 HRT / Assured / Non-Assured / Best-Effort 分层；**每周期按类优先服务**（HRT 先），类内按 CIR 债排序。

### 3.2 rate-count「CIR 债」（防饿死）

```352:366:gopon/common/dba/swbwm/allocator.go
			if _, seen := a.rateCount[k]; !seen {
				a.rateCount[k] = float64(cir) / 2.0
			}
			// Per-cycle CIR accrual: every cycle the allocator owes
			// this T-CONT one cycle's worth of CIR bytes.
			a.rateCount[k] += float64(cir)
			slots = append(slots, slot{
				job:    j,
				prof:   p,
				key:    k,
				demand: demandByAlloc[p.AllocID],
				cir:    cir,
				pir:    pir,
				debt:   a.rateCount[k],
			})
```

- 每周期给每个 T-CONT「记上」一份 CIR 债；有需求时优先偿还债务，**保证 Assured 不被饿死**——这就是 [参考模型](reference-model.md) 里「assured bandwidth restoration」的实现手段。

### 3.3 FEC 开销预留（RS(248,216) ≈ 14.8%）

```46:57:gopon/common/dba/swbwm/allocator.go
	// FecOverheadRatio: per RS(248, 216) on XGS-PON the relative
	// FEC overhead is (248-216)/216 = ~14.8 %. We reserve this off
	// the top of every per-T-CONT grant so the dashboard's reported
	// bandwidth matches what actually fits on the line.
	DefaultFecOverheadRatio = 32.0 / 216.0 // ≈ 0.148

	// BurstHeaderBytes is the per-ONU PLOu burst overhead at the
	// head of each upstream slot (preamble + delimiter + BIP + ONU-ID
	// + Ind + PLOAMu). G.984.3 = ~7 bytes, G.987.3 = ~52 bytes; the
	// number below matches the liteaggregator XGS-PON default.
	DefaultBurstHeaderBytes uint64 = 52
```

- 把 [FEC 开销](../01-protocol-stack/fec-principles.md) 与 **PLOu 突发头**从 PON 预算里扣掉，使「报告带宽=上线带宽」；按 [代际](../01-protocol-stack/gpon-vs-xgspon.md) 不同 FEC 比率不同（`fecRatioFor` 据 burst profile 区分 RS(255,239)/RS(248,216)/+LDPC）。

### 3.4 BWmap 字节→字、并按硬件合并表项

```719:725:gopon/common/dba/swbwm/allocator.go
func bytesToWordsCeil(b uint32, wordSize uint8) uint32 {
	if wordSize <= 1 {
		return b
	}
	w := uint32(wordSize)
	return (b + w - 1) / w
}
```

- 字长对应 [BWmap 格式](dbru-bwmap-format.md)：2.5G 上行 **4B/word**、10G 上行 **16B/block**（见 [GPON vs XGS-PON](../01-protocol-stack/gpon-vs-xgspon.md)）；
- `packBwMap`/`coalesceHdrRun` 把相邻同键表项合并——真实 OLT 硅片也这样压缩 BWmap 条目（每条约 8B 线开销）。

## 4. C++ 源头：liteaggregator 内核

### 4.1 grant_rate 与 1024 周期节律

```51:59:liteaggregator/src/glob/swbwm/src/grant_allocation/grant_bucket_accumulation.cpp
        rate_count += ext_prov_table->grant_rate[tcont_idx];

        // The value 1024 is historical. It expresses how often a TCONT will be allocated a grant.
        // E.g. if a TCONT's grant_rate is 1024, then it will be allocated a grant in
        // 1024/1024=100% of the frames. A TCONT with grant_rate 1 will only be allocated
        // in 1/1024~=0.1% of the frames.
        // We haven't given a name to this constant, as it is only used here, and it was difficult
        // to find a suitable name :)
        if (rate_count >= 1024) {
```

- C++ 里 `rate_count += grant_rate` 与 gopon 的 `a.rateCount[k] += cir` 是**同一思想**：用累加器控制「多久给一次 grant」。`grant_rate=1024` → 每帧都给；`=1` → 千分之一帧才给。gopon 用 bps×周期做了等价抽象。

### 4.2 FEC 码字装填（fec.cpp）

`codeword_blocks_to_data_and_fec_blocks` 求「一个码字里在扣掉 FEC 校验与突发头后，能放多少 payload block」——这是 [FEC](../01-protocol-stack/fec-principles.md) RS 码字「216 数据 + 32 校验」在调度侧的精确换算（C++ 用二分搜索，因 `toBlocks`/`FEC` 含 ceil 无解析解）。gopon 用一个 `fecRatio` 比例近似同一件事。

## 5. 一图总结：标准 → Go → C++

| 标准概念 | gopon | liteaggregator C++ |
|----------|-------|--------------------|
| T-CONT + Traffic Descriptor | `TcontProfile{Policy,Cir,Pir}` | `tcont_provisioning_table` |
| CIR 保底/债 | `rateCount[k] += cir` | `rate_count += grant_rate` |
| 策略分级 HRT/Assured/NA/BE | `PolicyClass` / `classOf` | `priority_level` |
| FEC 开销 | `FecOverheadRatio`/`fecRatioFor` | `fec.cpp` codeword fit |
| BWmap word/block | `bytesToWordsCeil` | `map_gen_*` |
| 突发头 PLOu | `BurstHeaderBytes=52` | `update_onu` / 头预留 |
| BWmap 合并 | `packBwMap`/`coalesceHdrRun` | HW BWMap 寄存器合并 |

## 来源

- **代码**（工作区）：
  - `gopon/common/dba/scheduler.go`（`TcontProfile`、`Schedule` DBA-lite、`effectiveWeight`）。
  - `gopon/common/dba/swbwm/allocator.go`（`Allocator`：四策略类、rate-count 债、FEC/突发头预留、`bytesToWordsCeil`、`packBwMap`/`coalesceHdrRun`）。
  - `liteaggregator/src/glob/swbwm/src/grant_allocation/grant_bucket_accumulation.cpp`（`grant_rate`/1024 节律）、`fec.cpp`（码字装填）。
- **公有标准**：ITU-T G.988（T-CONT 类 262 / Traffic Descriptor 类 280 / policy）、G.9807.1（FEC RS(248,216)、BWmap word/block 粒度、PLOu 突发头）。
- 说明：gopon 为「内核移植/教学实现」，与生产 C++ 在帧节律建模上有差异（详见 `allocator.go` 顶部注释）；标准语义以 ITU-T 原文为准。
