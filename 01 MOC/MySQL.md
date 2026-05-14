---
type: moc
domain: database
topic:
  - mysql
status: active
source: "[[MySQL学习笔记]]"
created: 2026-05-09
updated: 2026-05-09
current_focus:
---

# MySQL

## 当前重点
这段时间优先推进什么？不要超过 3 项。

- 


## 学习目标
从开发者视角理解 MySQL 如何处理查询、索引、事务、锁、日志和并发控制，并能把这些知识用于 SQL 优化、故障排查和系统设计。

## 核心问题
```text
1. 查询为什么慢，如何定位？
2. 索引为什么能加速查询，什么时候会失效？
3. 并发事务如何保持正确性？
4. InnoDB 如何通过锁、日志和 MVCC 平衡性能与一致性？
```

## 学习路径
1. 先掌握 [[MySQL 索引]]、[[B+Tree 索引]]、[[聚簇索引与二级索引]]。
2. 再学习 [[覆盖索引与回表]]、[[MySQL 最左前缀法则]]、[[MySQL 索引失效]]。
3. 然后学习 [[MySQL 事务]]、[[ACID]]、[[MySQL 事务隔离级别]]。
4. 最后学习 [[InnoDB 锁]]、[[Redo Log]]、[[Undo Log]]、[[MVCC]]。

## 查询与优化
- [[MySQL 慢查询]]
- [[EXPLAIN]]
- [[MySQL 索引]]
- [[MySQL 索引失效]]

## 索引体系
- [[B+Tree 索引]]
- [[聚簇索引与二级索引]]
- [[覆盖索引与回表]]
- [[MySQL 最左前缀法则]]

## 事务与并发
- [[MySQL 事务]]
- [[ACID]]
- [[MySQL 事务隔离级别]]
- [[InnoDB 锁]]
- [[MVCC]]

## 日志与恢复
- [[Redo Log]]
- [[Undo Log]]

## 和现有笔记的连接
- [[MySQL学习笔记]]
- [[数据库]]
- [[后端面试/数据库/数据库 MOC]]

## 当前薄弱点
- [ ] 把 SQL 优化章节继续拆成 `order by 优化`、`group by 优化`、`limit 优化`。
- [ ] 把主从复制、分库分表继续拆成分布式系统相关笔记。
- [ ] 为每个核心概念补一个真实 SQL 示例。


