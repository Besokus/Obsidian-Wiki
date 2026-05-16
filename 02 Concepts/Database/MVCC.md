---
type: concept
domain: database
topic:
  - mysql
  - innodb
  - transaction
  - concurrency-control
status: learning
difficulty: hard
mastery: seen
source: "[[MySQL学习笔记]]"
created: 2026-05-09
reviewed:
next_review:
---

# MVCC

## 一句话解释
MVCC 是多版本并发控制，通过维护数据的多个版本，让普通读操作不用阻塞写操作，从而提升并发性能。

## 它回答的核心问题
- 它试图解释：为什么读写并发时，普通查询不用总是加锁。
- 它试图约束：同一条记录的不同版本，哪个事务能看见。
- 它试图区分：快照读和当前读为什么行为不同。

## 前置知识
理解它之前最好先知道什么？

- [[MySQL 事务隔离级别]]
- [[Undo Log]]
- [[InnoDB 锁]]

## 它解决什么问题
- 读写互相阻塞会降低数据库并发能力。
- 事务需要在不同隔离级别下看到合适的数据版本。
- 快照读需要读取事务开始时或语句开始时可见的数据。

## 核心机制
```text
1. 每行记录包含隐藏字段，记录事务信息和回滚指针。
2. update/delete 产生 undo log，形成历史版本链。
3. 快照读创建 ReadView。
4. 根据 ReadView 判断版本链中哪个版本对当前事务可见。
```

## 重点部分详解
- 机制细节：MVCC 的关键不是“多版本”这个词，而是 ReadView 如何决定版本可见性。
- 常见故障：长事务会拖住历史版本，导致 undo 清理变慢。
- 典型代价：读写不互相阻塞的代价是维护版本链和可见性判断成本。
- 实战判断：普通 select 和 `for update`、`update` 看到的数据可能不同，这是 MVCC + 锁共同作用的结果。

## 当前读与快照读
- 当前读：读取最新版本，并可能加锁，例如 `select ... for update`、`update`、`delete`。
- 快照读：普通 `select`，读取可见版本，不加锁，通常不会阻塞写。

## 不同隔离级别下的快照
- Read Committed：每次 `select` 都生成新的 ReadView。
- Repeatable Read：事务中第一次 `select` 生成 ReadView，后续复用。
- Serializable：快照读会退化为当前读。

## 开发者视角
- 普通查询和加锁查询看到的数据可能不同。
- 长事务会导致旧版本无法及时清理。
- 需要结合 [[MySQL 事务隔离级别]]、[[Undo Log]] 和 [[InnoDB 锁]] 理解。

## 工程判断
遇到真实问题时，如何判断是否该用它？需要看哪些证据？

- 判断信号：
- 关键权衡：
- 常见替代方案：

## 常见误解
- 

## 易混概念
- [[Undo Log]]：MVCC 的历史版本来源。
- [[InnoDB 锁]]：MVCC 减少读写冲突，但不能替代所有锁。
- [[MySQL 事务隔离级别]]：决定 ReadView 创建时机和可见性规则。

## 相关笔记
- [[ACID]]
- [[MySQL 事务]]

## 我的理解
MVCC 的价值在于让“读”不必总是等“写”。但它不是无成本的，历史版本、ReadView 和长事务都会带来额外复杂度。

## 待补充
- [ ] 补充 ReadView 四个字段和版本可见性判断规则。


