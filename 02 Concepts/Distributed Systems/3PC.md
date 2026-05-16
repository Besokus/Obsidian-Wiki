---
type: concept
domain: distributed-systems
topic:
  - distributed-transaction
  - atomic-commit
status: learning
difficulty: hard
mastery: seen
created: 2026-05-16
reviewed:
next_review:
aliases:
  - Three-Phase Commit
  - 3PC
  - 三阶段提交
---

# 3PC（Three-Phase Commit）

## 一句话解释
3PC 在 2PC 基础上增加一个中间阶段，试图减少“参与者进入不确定状态后只能阻塞等待”的概率。

> 现实里在异步网络/分区模型下仍难以彻底保证安全且复杂度更高，所以工程中通常“了解即可”。

---

# 1. 三个阶段（直觉）
```text
CanCommit  ->  PreCommit  ->  DoCommit
```

- **CanCommit**：询问是否能提交（类似 2PC 的投票前置）。
- **PreCommit**：参与者进入“预提交”状态（比 2PC 更细分的中间态）。
- **DoCommit**：最终提交指令。

3PC 的核心思路是把“不确定”窗口切小，并引入超时策略让参与者能做出本地决策。

---

# 2. 为什么工程上少用
- 对网络/时钟假设更强（在真实异步网络 + 分区下仍可能出现不安全边界）
- 实现与运维复杂度更高
- 很多场景更倾向选择：[[TCC]] / [[Saga]] / [[Outbox Pattern]] + [[事务状态机]]

---

## 相关笔记
- 对比：[[2PC]]
- 总览：[[分布式事务（入门整理）]]

