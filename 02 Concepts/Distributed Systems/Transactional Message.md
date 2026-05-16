---
type: concept
domain: distributed-systems
topic:
  - distributed-transaction
  - reliable-messaging
  - transactional-message
status: learning
difficulty: hard
mastery: seen
created: 2026-05-16
reviewed:
next_review:
aliases:
  - Transactional Message
  - 事务消息
---

# 事务消息（Transactional Message）

## 一句话解释
事务消息解决的是“本地事务 + 发消息不原子”的问题，但把协议与回查机制放到 MQ/Broker 侧：先发半消息（half message），本地事务结束后再提交/回滚消息；若状态不明，Broker 通过回查确认。

---

# 1. 典型流程（直觉）
```text
1) Producer send half message (prepare)
2) Execute local transaction
3) Commit/Rollback message
4) Broker check / callback (when uncertain)
```

## 2. 关键点（必须记住）
- 超时/分区导致“状态不确定”时，Broker 会回查 → 服务端必须能根据事务记录给出一致回答（通常依赖 [[事务状态机]]）
- 依然可能重复投递 → 消费者仍需要 [[幂等性]]

---

# 3. Outbox vs 事务消息（工程对比）
- [[Outbox Pattern]]：依赖 DB + 自建 relay；简单通用，但有 polling/积压/清理治理成本。
- 事务消息：依赖 MQ 支持；减少自建组件，但更绑定 MQ 语义与运维复杂度。

---

## 相关笔记
- 总览：[[分布式事务（入门整理）]]
- 对比：[[Outbox Pattern]]
- 底座：[[幂等性]]、[[事务状态机]]、[[对账]]

