---
type: concept
domain: distributed-systems
topic:
  - distributed-transaction
  - reliable-messaging
  - outbox
status: learning
difficulty: hard
mastery: seen
created: 2026-05-16
reviewed:
next_review:
aliases:
  - Transactional Outbox
  - Outbox
  - Outbox Pattern
  - 本地消息表
---

# Transactional Outbox（Outbox Pattern）

## 一句话解释
Outbox Pattern 解决的是“**本地事务提交** 与 **消息发送** 之间不原子”的问题：把要发送的消息先写入本地 outbox 表，和业务数据在同一个 DB 事务里提交，再异步投递到 MQ。

---

# 1. 经典问题：本地事务 + MQ 为什么会错
如果你直接做：
```text
COMMIT 本地事务
send MQ
```
可能出现：
- 本地事务成功，但消息没发出去（丢消息）
- 消息发出去了，但本地事务回滚（脏消息）

---

# 2. Outbox 的流程（必须会画）

```text
业务服务：
  BEGIN
    update business tables
    insert outbox(message)
  COMMIT

Relay/Worker：
  poll/scan outbox -> publish to MQ -> mark sent
```

---

# 3. 你必须接受的三个现实（重点细节）

## 3.1 重复（At-least-once）
Relay 可能重复投递（重试/超时/崩溃恢复），因此：
- 消费者必须实现 [[幂等性]]
- 需要 message id / 去重 key

## 3.2 延迟（Polling / Backlog）
轮询间隔、积压、限流都会引入延迟 → 业务要容忍“最终一致”，并有监控告警。

## 3.3 清理（Retention）
outbox 表会增长，需要归档/清理策略（TTL、分区、归档表）。

---

# 4. 变体与工程实现提示
- Relay 可以是轮询，也可以是 CDC（binlog）驱动（降低轮询成本）
- 需要考虑消息顺序：同 key 的有序投递通常要靠分区 key + 消费端处理
- 仍需要 [[事务状态机]] / [[对账]] 做兜底（“消息永远没发出去”的长尾）

---

## 相关笔记
- 消息系统：[[消息队列]]
- 对比：[[Transactional Message]]
- 总览：[[分布式事务（入门整理）]]
- 底座：[[幂等性]]、[[对账]]

