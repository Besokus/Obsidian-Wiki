---
type: concept
domain: database
topic:
  - mysql
  - transaction
  - isolation
status: learning
difficulty: medium
mastery: seen
source: "[[MySQL学习笔记]]"
created: 2026-05-09
reviewed:
next_review:
---

# MySQL 事务隔离级别

## 一句话解释
事务隔离级别定义并发事务之间能看到彼此修改的程度。隔离越强，数据异常越少，但并发性能通常越低。

## 前置知识
理解它之前最好先知道什么？

- [[]]

## 它解决什么问题
- 脏读：读到其他事务未提交的数据。
- 不可重复读：同一事务内两次读取同一行，结果不同。
- 幻读：同一事务内按条件查询，前后出现新的匹配行。

## 核心机制
| 隔离级别 | 脏读 | 不可重复读 | 幻读 | 说明 |
|---|---|---|---|---|
| Read Uncommitted | 可能 | 可能 | 可能 | 性能高但安全性差 |
| Read Committed | 不会 | 可能 | 可能 | 每次读看到已提交版本 |
| Repeatable Read | 不会 | 不会 | 理论可能，InnoDB 通过 MVCC/锁处理常见场景 | MySQL 默认 |
| Serializable | 不会 | 不会 | 不会 | 隔离最强，并发最低 |

## 开发者视角
- 查看当前隔离级别：`SELECT @@TRANSACTION_ISOLATION;`
- 设置隔离级别：`SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;`
- MySQL 默认使用 Repeatable Read，要结合 [[MVCC]] 和 [[InnoDB 锁]] 理解。
- 隔离级别不是越高越好，核心是业务能接受什么一致性与性能权衡。

## 使用场景
- 账户、库存等强一致写入场景，需要明确事务边界和锁行为。
- 报表、读多写少场景，可能更关注读性能和可接受的数据新鲜度。

## 工程判断
遇到真实问题时，如何判断是否该用它？需要看哪些证据？

- 判断信号：
- 关键权衡：
- 常见替代方案：

## 常见误解
- 

## 易混概念
- [[MVCC]]：减少读写冲突的实现机制。
- [[InnoDB 锁]]：当前读和写操作的重要隔离手段。
- [[ACID]]：隔离级别对应 ACID 中的 Isolation。

## 相关笔记
- [[MySQL 事务]]
- [[Undo Log]]

## 我的理解
隔离级别是数据库把并发复杂度暴露给开发者的一个旋钮。写业务代码时，如果不知道当前隔离级别，就很难判断一次读取到底代表“当前最新值”还是“事务快照里的值”。

## 待补充
- [ ] 用两个会话演示脏读、不可重复读和幻读。



