---
type: concept
domain: database
topic:
  - mysql
  - index
  - query-optimization
status: learning
difficulty: medium
mastery: seen
source: "[[MySQL学习笔记]]"
created: 2026-05-09
reviewed:
next_review:
---

# MySQL 最左前缀法则

## 一句话解释
最左前缀法则是联合索引的使用规则：查询条件要从联合索引最左列开始，并且不能跳过中间列。它来自 B+Tree 联合索引的排序方式。

## 前置知识
理解它之前最好先知道什么？

- [[]]

## 它解决什么问题
- 判断联合索引是否能被完整使用。
- 指导多条件查询的索引设计。
- 避免建立了联合索引但查询仍然低效。

## 核心机制
```text
1. 联合索引按第一列排序。
2. 第一列相同时，再按第二列排序。
3. 只有前面的列确定后，后面的列才有序可用。
```

## 开发者视角
- 建联合索引时，把高频过滤条件和区分度高的字段放在合适位置。
- 查询跳过左侧列时，右侧列通常无法充分利用索引。
- 范围查询右侧的列可能无法继续使用索引。
- 实际是否使用索引，仍要用 [[EXPLAIN]] 验证。

## 工程判断
遇到真实问题时，如何判断是否该用它？需要看哪些证据？

- 判断信号：
- 关键权衡：
- 常见替代方案：

## 最小例子
```sql
create index idx_user_pro_age_status on tb_user(profession, age, status);
```

通常能利用索引的查询：
```sql
where profession = '软件工程'
where profession = '软件工程' and age = 31
where profession = '软件工程' and age = 31 and status = '0'
```

跳过 `profession` 直接查 `age`，通常无法完整使用该联合索引。

## 常见误解
- 

## 易混概念
- [[B+Tree 索引]]：最左前缀法则来自 B+Tree 的有序组织。
- [[覆盖索引与回表]]：联合索引既影响过滤，也可能覆盖返回列。
- [[MySQL 索引失效]]：违反最左前缀是索引失效或部分失效的常见原因。

## 相关笔记
- [[MySQL 索引]]
- [[MySQL 慢查询]]

## 我的理解
最左前缀法则不是 MySQL 的任性规则，而是联合索引排序方式的自然结果。理解排序方式后，设计索引会比背规则更可靠。

## 待补充
- [ ] 补充范围查询导致右侧列失效的 EXPLAIN 示例。


