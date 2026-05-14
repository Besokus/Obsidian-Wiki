---
type: concept
domain: database
topic:
  - mysql
  - transaction
status: learning
difficulty: medium
mastery: seen
source: "[[MySQL学习笔记]]"
created: 2026-05-09
reviewed:
next_review:
---

# MySQL 事务

## 一句话解释
事务是一组要么全部成功、要么全部失败的数据库操作。它把多条 SQL 包装成一个逻辑工作单元，避免系统只执行了一半就留下错误状态。

## 它解决什么问题
- 正确性：避免转账、扣库存、创建订单这类操作只完成一部分。
- 一致性：保证业务约束在事务前后仍然成立。
- 故障恢复：执行失败时可以回滚到事务开始前。
- 并发控制：配合 [[MySQL 事务隔离级别]] 和 [[InnoDB 锁]] 控制并发读写的影响。

## 前置知识
理解它之前最好先知道什么？

- [[]]

## 什么时候用
- 多条 SQL 必须作为一个整体提交。
- 写操作之间存在业务依赖，例如扣余额后必须加余额。
- 需要在失败时自动回滚中间状态。

## 什么时候不用
- 单条独立 SQL 已经满足原子性时，不要为了形式强行扩大事务。
- 长时间事务要谨慎，因为会持有锁、保留 [[Undo Log]]，影响并发和 MVCC 清理。

## 核心机制
```text
1. 开启事务：START TRANSACTION 或 BEGIN。
2. 执行业务 SQL。
3. 成功则 COMMIT，失败则 ROLLBACK。
```

## 开发者视角
- 默认自动提交可以通过 `SELECT @@AUTOCOMMIT;` 查看。
- 手动事务中，异常路径必须显式回滚。
- 事务范围要尽量短，不要把网络调用、用户交互、长时间计算放进事务。
- 线上排查事务问题时，要关注锁等待、慢 SQL、长事务和隔离级别。

## 工程判断
遇到真实问题时，如何判断是否该用它？需要看哪些证据？

- 判断信号：
- 关键权衡：
- 常见替代方案：

## 最小例子
```sql
start transaction;
update account set money = money - 1000 where name = '张三';
update account set money = money + 1000 where name = '李四';
commit;
```

如果第二条更新失败，应执行 `rollback`，避免只扣钱不加钱。

## 常见误解
- 

## 易混概念
- [[ACID]]：事务应该满足的性质。
- [[MySQL 事务隔离级别]]：并发事务之间互相可见的规则。
- [[InnoDB 锁]]：隔离性的重要实现手段。

## 相关笔记
- [[Undo Log]]
- [[Redo Log]]
- [[MVCC]]

## 我的理解
事务不是“多条 SQL 的容器”这么简单，它本质上是在复杂失败场景下保护业务状态的一种边界。开发时最重要的是把事务边界划准，而不是把所有操作都包进去。

## 待补充
- [ ] 补充 Spring / C++ 服务中事务边界的真实例子。

