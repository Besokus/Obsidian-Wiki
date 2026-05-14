---
tags: [database]
source: "[[后端岗位面试题]]"
---

# MySQL 有哪些隔离级别？怎么解决幻读？

四种隔离级别：**读未提交、读已提交、可重复读、串行化**。

![image-20260124144230493](C:/Users/Knight/AppData/Roaming/Typora/typora-user-images/image-20260124144230493.png)

![image-20260124144623231](C:/Users/Knight/AppData/Roaming/Typora/typora-user-images/image-20260124144623231.png)



**脏读**：防止读取到其他未提交事务修改的数据。



**不可重复读**：在**同一个事务**中，对**同一行记录**进行两次查询，**得到的结果不一致**。

原因是：
在两次查询之间，**其他事务修改并提交了该行数据**。



**幻读**：在**同一个事务**中，用**相同的查询条件**查询多次，**返回的记录行数不同**（本质：出现了新行）。

原因是：
在两次查询之间，**其他事务插入或删除了满足条件的新记录并提交**。



解决**幻读**主要有两种方式：

**方式一：设置隔离级别为“串行化” (Serializable)**

这是最简单粗暴的方式。它会给事务的读取操作（`SELECT`）自动加上锁，阻止其他事务插入任何可能影响查询结果的新行。但如前所述，**这会严重牺牲并发性**，不推荐使用。



**方式二：在“可重复读” (RR) 级别下解决（InnoDB的实现）**

这才是面试的重点。**MySQL的InnoDB引擎，在其默认的“可重复读”（RR）级别下，就已经在很大程度上解决了幻读问题。**

针对“**快照读**”：使用 MVCC (多版本并发控制)

就是我们最常用的普通 `SELECT` 语句（不带 `FOR UPDATE` 或 `LOCK IN SHARE MODE`）

**MVCC + 快照读**，并不是阻止了幻读的发生，而是让产生的幻行对当前事务不可见。

InnoDB 中 MVCC 始终存在，简单 SELECT 默认就是快照读。但如果不显式开启事务，每条 SELECT 默认都是一个独立事务，并自动提交，会生成新的 Read View，因此无法避免幻读。只有在显式事务中，快照读才能保证多次查询结果一致。

```mysql
-- 显式事务
BEGIN;
SELECT * FROM orders WHERE amount > 100; -- 3 行
-- 其他事务插入 amount=200
SELECT * FROM orders WHERE amount > 100; -- 仍然 3 行
```



针对“**当前读**”：使用 Next-Key Locks (临键锁)

包括：`SELECT ... FOR UPDATE` (排他锁)、`SELECT ... LOCK IN SHARE MODE` (共享锁)，以及所有的 `INSERT`、`UPDATE`、`DELETE` 操作。

这些操作**必须**读取数据库**最新**的已提交版本，并且要加锁。

```mysql
BEGIN;

SELECT * 
FROM orders 
WHERE amount > 100 
FOR UPDATE; -- amount > 100 的索引范围被加 Next-Key Lock

COMMIT;
```

UPDATE / DELETE 天然是**当前读**，自动对匹配范围加 Next-Key Lock



**总结**：InnoDB 在默认的 **RR** 级别下，通过 **MVCC** 机制解决了“快照读”的幻读问题；通过 **Next-Key Lock (临键锁)** 机制解决了“当前读”的幻读问题。
