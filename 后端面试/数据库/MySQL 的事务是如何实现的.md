---
tags: [database]
source: "[[后端岗位面试题]]"
---

# MySQL 的事务是如何实现的？

InnoDB 通过「日志系统（redo/undo）」+「锁机制」+「MVCC（多版本并发控制）」三大核心组件，实现了事务的 ACID 特性，**其中 redo log 保证持久性、undo log 保证原子性和隔离性、锁 + MVCC 保证隔离性、本身的设计保证一致性。**
