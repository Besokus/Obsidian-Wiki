---
type: concept
domain: database
topic:
  - mysql
  - query-optimization
status: learning
difficulty: medium
mastery: seen
source: "[[MySQL学习笔记]]"
created: 2026-05-09
reviewed:
next_review:
---

# EXPLAIN

## 一句话解释
EXPLAIN 用来查看 MySQL 如何执行一条 SELECT 语句，包括访问顺序、连接类型、可能使用的索引、实际使用的索引和扫描行数估计。

## 前置知识
理解它之前最好先知道什么？

- [[]]

## 它解决什么问题
- 判断 SQL 有没有走索引。
- 判断查询是否可能全表扫描。
- 判断联合查询、子查询的大致执行顺序。
- 为慢查询优化提供证据。

## 核心字段
| 字段 | 作用 |
|---|---|
| id | 查询执行序号，id 越大通常越先执行 |
| select_type | 查询类型，例如 SIMPLE、PRIMARY、UNION、SUBQUERY |
| type | 连接类型，常用于判断访问效率 |
| possible_keys | 可能使用的索引 |
| key | 实际使用的索引 |
| key_len | 使用到的索引字节长度 |
| rows | 预计扫描行数 |
| filtered | 过滤后保留行的比例估计 |
| Extra | 额外信息，例如 Using index、Using where |

## 开发者视角
- 重点先看 `type`、`key`、`rows`、`Extra`。
- `key` 为 NULL 通常说明没有使用索引。
- `rows` 很大时，要检查过滤条件和索引设计。
- EXPLAIN 是估计值，不等于真实性能测试，但足以指导第一轮优化。

## 工程判断
遇到真实问题时，如何判断是否该用它？需要看哪些证据？

- 判断信号：
- 关键权衡：
- 常见替代方案：

## 最小例子
```sql
explain select * from tb_user where phone = '17799990015';
```

## 常见误解
- 

## 易混概念
- [[MySQL 慢查询]]：慢查询发现问题，EXPLAIN 分析执行计划。
- [[MySQL 索引]]：EXPLAIN 可验证索引是否被使用。
- [[MySQL 索引失效]]：索引失效通常会在 key、type、rows 中体现。

## 相关笔记
- [[覆盖索引与回表]]
- [[MySQL 最左前缀法则]]

## 我的理解
EXPLAIN 是 SQL 优化的显微镜。没有执行计划就谈优化，基本是在猜。

## 待补充
- [ ] 补充 type 从好到差的完整含义。


