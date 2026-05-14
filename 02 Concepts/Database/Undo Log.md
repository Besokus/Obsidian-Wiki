---
type: concept
domain: database
topic:
  - mysql
  - innodb
  - log
  - transaction
status: learning
difficulty: medium
mastery: seen
source: "[[MySQL学习笔记]]"
created: 2026-05-09
reviewed:
next_review:
---

# Undo Log

## 一句话解释
Undo Log 是回滚日志，记录数据被修改前的信息，用于事务回滚和 [[MVCC]] 快照读。

## 前置知识
理解它之前最好先知道什么？

- [[]]

## 它解决什么问题
- 事务失败时，需要把已经修改的数据恢复回去。
- 快照读需要读取旧版本数据，不能只看最新行。

## 核心机制
```text
1. insert/update/delete 时生成 undo log。
2. rollback 时根据 undo log 反向恢复。
3. MVCC 快照读可能沿着 undo log 版本链读取旧版本。
4. 当没有事务需要旧版本后，undo log 才能清理。
```

## 开发者视角
- 长事务会导致旧版本长期不能清理，增加存储和性能压力。
- Undo Log 支撑 [[ACID]] 中的原子性，也支撑 [[MVCC]]。
- 排查历史版本膨胀、长事务问题时要关注 undo 保留。

## 例子
- 删除一行时，Undo Log 可以记录相反的插入信息。
- 更新一行时，Undo Log 可以记录更新前的旧值。

## 工程判断
遇到真实问题时，如何判断是否该用它？需要看哪些证据？

- 判断信号：
- 关键权衡：
- 常见替代方案：

## 常见误解
- 

## 易混概念
- [[Redo Log]]：Redo 是物理重做，Undo 是逻辑回滚。
- [[MVCC]]：MVCC 依赖 Undo Log 构造历史版本。
- [[MySQL 事务]]：事务回滚依赖 Undo Log。

## 相关笔记
- [[MySQL 事务隔离级别]]
- [[InnoDB 锁]]

## 我的理解
Undo Log 不只是“回滚用的日志”。它也是快照读能看到历史版本的基础，所以长事务会影响的不只是当前事务本身。

## 待补充
- [ ] 补充 undo log 版本链示意。



