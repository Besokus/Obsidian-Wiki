---
type: concept
domain: distributed-systems
topic:
  - pacelc
  - cap
  - consistency
  - availability
  - latency
status: learning
difficulty: medium
mastery: seen
source:
  - "https://en.wikipedia.org/wiki/CAP_theorem"
  - "https://javaguide.cn/distributed-system/protocol/cap-and-base-theorem.html"
created: 2026-05-14
reviewed:
next_review:
---

# PACELC

## 一句话理解
**PACELC 是对 CAP 的补充扩展**：如果发生网络分区（P），系统要在可用性（A）和一致性（C）之间取舍；否则（E，网络正常时），系统也要在延迟（L）和一致性（C）之间取舍。

```text
If Partition happens:  A vs C
Else (no partition):   L vs C
```

## 为什么需要 PACELC
CAP 把焦点放在“分区发生时”的硬约束上，但很多系统在绝大多数时间里并没有明显分区，却仍然需要在：

- 更低延迟（更快响应、更高吞吐）
- 更强一致（更强读写语义、更少业务补偿）

之间做工程取舍。PACELC 用 “Else: L vs C” 把这部分现实补齐。

## 拆开理解：P 和 E 两段
### P：分区发生时（PA / PC）
- **PA（Partition + Availability）**：分区时尽量可用，但可能返回旧数据/产生冲突，需要后续收敛。
- **PC（Partition + Consistency）**：分区时优先一致语义，拒绝/等待部分请求，牺牲可用性。

### E：分区未发生时（EL / EC）
- **EL（Else + Latency）**：平时偏低延迟（弱一些的一致性语义，或接受更短暂的不一致窗口）。
- **EC（Else + Consistency）**：平时偏强一致（为一致性付出更多延迟/协调成本）。

## 四类常见倾向（不是“系统标签”）
PACELC 常见的四种组合是“设计维度”，更适合用来分析某个操作路径/读写策略，而不是给整个系统贴死标签：

- **PA/EL**：分区时保可用，平时也优先低延迟。常见于对“快和不断服务”更敏感的读多写少场景（通常配最终一致与补偿机制）。
- **PC/EC**：分区时保一致，平时也偏强一致。常见于协调系统、元数据、配置、锁等“宁可失败也不能错”的路径。
- **PA/EC**：分区时保可用，但平时尽量一致（例如提供“强读/弱读”两种读路径，默认强一些的一致性语义）。
- **PC/EL**：分区时保一致，但平时尽量低延迟（例如局部强一致、读写分离与可调一致性级别并存）。

## CAP vs PACELC
- CAP：强调“**P 发生时**，C 与 A 不可兼得”。
- PACELC：在 CAP 的基础上补充“**P 不发生时**，L 与 C 仍然要取舍”。

## 工程判断框架（两组问题）
### 分区时（P）
- 这类请求在网络分区时：宁可失败/超时，还是宁可返回可能过期的数据？
- 如果选择 PA：冲突与不一致如何收敛（幂等、重试、补偿、对账、冲突解决策略）？

### 正常时（E）
- 这条链路对延迟的预算是多少？尾延迟（p95/p99）是否关键？
- 一致性语义要强到什么程度（线性一致、读己之写、因果一致、最终一致）？

## 常见误区
- “PACELC 替代 CAP”：不是替代，是扩展补充。
- “低延迟一定意味着弱一致”：可以通过局部同步、读策略、缓存一致性机制等做折中，但总体上更强一致通常需要更多协调与更高延迟成本。
- “一个系统只能属于一种类型”：同一系统不同操作（强读/弱读、事务/非事务、跨区/同区）可能对应不同取舍。

## 相关笔记
- [[CAP]]
- [[数据一致性]]
- [[网络分区]]
- [[网络不可靠]]
- [[分布式系统中的复制]]

