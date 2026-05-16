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

## 一句话解释
PACELC 是对 CAP 的补充：发生分区时在可用性和一致性之间取舍；不发生分区时，系统仍然要在延迟和一致性之间取舍。

## 它回答的核心问题
- 它试图解释：为什么没有分区时，系统设计也依然有权衡。
- 它试图约束：读取路径、写入路径和复制路径都可能影响延迟与一致性。
- 它试图区分：CAP 只看分区时，PACELC 把正常路径也纳入分析。

## 前置知识
理解它之前最好先知道什么？

- [[CAP]]
- [[BASE]]
- [[线性一致性]]
- [[最终一致性]]

## 它解决什么问题
- 补齐 CAP 对“正常时延和一致性”权衡的忽略。
- 说明为什么很多系统即使不分区也会为低延迟做妥协。
- 帮助分析复制、读写策略和一致性等级的真实成本。

## 重点部分详解
- P 时刻：在可用性和一致性之间取舍。
- E 时刻：在延迟和一致性之间取舍。
- PACELC 不是替代 CAP，而是把 CAP 的现实工程面补完整。
- 同一个系统不同路径可以有不同权衡：强读、弱读、写路径、配置路径往往各不相同。

## 核心机制
```text
If Partition happens:  A vs C
Else (no partition):   L vs C
```

## 常见工程取向
- PA/EL：偏可用、偏低延迟，常见于用户体验优先系统。
- PC/EC：偏一致，常见于元数据、权限、协调类路径。
- PA/EC：分区时保可用，平时尽量保证更强一致。
- PC/EL：分区时保一致，平时尽量压低延迟。

## 易混概念
- [[CAP]]：只看分区时的取舍。
- [[BASE]]：更偏向最终收敛的工程实践。
- [[线性一致性]]：PACELC 中的 C 往往围绕它展开。

## 我的理解
PACELC 的价值是提醒你：系统设计不只有“坏的时候怎么选”，还有“平时怎么选”。很多工程问题其实出在日常路径的延迟与一致性折中，而不是灾难时刻。
