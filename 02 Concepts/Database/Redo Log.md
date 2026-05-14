---
type: concept
domain: database
topic:
  - mysql
  - innodb
  - log
status: learning
difficulty: medium
mastery: seen
source: "[[MySQL学习笔记]]"
created: 2026-05-09
reviewed:
next_review:
---

# Redo Log

## 一句话解释
Redo Log 是 InnoDB 的重做日志，记录事务提交时数据页的物理修改，用于崩溃恢复并保证事务持久性。

## 前置知识
理解它之前最好先知道什么？

- [[]]

## 它解决什么问题
- 数据页还没刷盘时数据库崩溃，提交过的修改不能丢。
- 直接随机写数据页成本高，需要用顺序写日志降低写入成本。

## 核心机制
```text
1. 修改数据页后，先写 redo log buffer。
2. 事务提交时保证 redo log 按策略持久化。
3. 后台再把脏页刷回磁盘。
4. 崩溃恢复时用 redo log 重放已提交修改。
```

## 开发者视角
- Redo Log 主要服务于 [[ACID]] 中的持久性。
- 它让数据库可以先顺序写日志，再异步刷脏页。
- 写入延迟和数据安全性与刷盘策略有关。

## 为什么不直接写数据页
写 Redo Log 是追加顺序写，写数据页通常是随机写。顺序写对磁盘更友好，因此可以提升事务提交路径的性能。

## 工程判断
遇到真实问题时，如何判断是否该用它？需要看哪些证据？

- 判断信号：
- 关键权衡：
- 常见替代方案：

## 常见误解
- 

## 易混概念
- [[Undo Log]]：Undo 负责回滚和 MVCC，Redo 负责崩溃恢复。
- [[ACID]]：Redo Log 主要保证 Durability。
- [[MySQL 事务]]：事务提交依赖日志机制保证可靠性。

## 相关笔记
- [[MVCC]]
- [[InnoDB 锁]]

## 我的理解
Redo Log 是数据库“先记账，再慢慢落盘”的核心机制。它把随机写数据页的问题转化成顺序写日志的问题。

## 待补充
- [ ] 补充 redo log buffer、redo log file 和刷盘参数。



