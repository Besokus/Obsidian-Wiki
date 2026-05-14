---
type: concept
domain: distributed-systems
topic:
  - base
  - availability
  - eventual-consistency
  - reliability
status: learning
difficulty: easy
mastery: seen
source:
  - "https://javaguide.cn/distributed-system/protocol/cap-and-base-theorem.html"
created: 2026-05-14
reviewed:
next_review:
---

# BASE

## 一句话理解
**BASE 是面向高可用分布式系统的一种工程思想**：系统不追求每一时刻都强一致，而是允许短暂不一致，通过后续同步、修复与收敛，最终达到一致。

BASE =：

```text
Basically Available
Soft State
Eventually Consistent
```

## 1. Basically Available（基本可用）
系统出现故障或压力时，尽量“还能用”，但允许以可控方式降级，而不是直接整体不可用。

常见表现：
- 响应变慢、排队、限流
- 返回“可接受的降级结果”（部分功能不可用、弱化一致性读）
- 异步处理、稍后完成（例如先受理再最终落库）

## 2. Soft State（软状态）
系统状态允许在一段时间内“非严格稳定”，例如：

- 多副本之间存在传播延迟
- 缓存、索引、派生数据可能短暂过期
- 通过后台同步/重算/修复让状态逐步接近正确

## 3. Eventually Consistent（最终一致）
在没有新写入的前提下，系统保证最终会收敛到一致状态，但不保证任意时刻都一致。

“最终一致”是目标，关键在于“靠什么收敛”。

## BASE 常用工程机制（让系统可收敛）
- **消息队列/事件驱动**：把强同步链路拆成异步阶段，提升可用性与吞吐。
- **幂等性**：保证重复请求/重复消费不会造成多次副作用。
- **重试**：处理偶发失败，但要配合退避、限流与最大重试策略。
- **补偿（SAGA/TCC 的一部分思想）**：失败后用反向操作修复业务状态。
- **对账/修复任务**：用离线或周期任务发现并修复不一致。
- **版本号/时间戳/向量时钟（按需）**：用于冲突检测与解决。
- **状态机**：用显式状态流转减少“半成功”带来的不确定性。

## BASE 与 ACID（直觉对比）
- ACID 更偏单库/强事务语义，强调强一致与隔离。
- BASE 更偏分布式与高可用目标，接受短暂不一致，用工程手段保证最终正确。

这不是非此即彼：同一系统常见做法是“核心链路强一致，边缘链路 BASE”。

## BASE、CAP、PACELC 的关系（直觉）
- BASE 常与偏 AP 的系统取舍更贴近（分区时优先可用，允许短暂不一致）。
- PACELC 提醒：即使不分区，延迟与一致性仍有权衡；很多 BASE 设计也在做 “Else: L vs C” 的选择。

## 相关笔记
- [[CAP]]
- [[PACELC]]
- [[数据一致性]]
- [[幂等性]]
- [[重试]]
- [[降级]]
- [[熔断]]

