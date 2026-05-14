---
tags: [database]
source: "[[后端岗位面试题]]"
---

# MySQL中锁粒度

> **锁粒度是指数据库在并发控制中，加锁所覆盖的数据范围大小。**
>
> InnoDB 的**锁粒度**由索引决定，有索引时可以精确到记录或区间；没有索引时会退化为**表级锁**效果。

- 粒度越小 → 并发性越高 → 管理成本越大
- 粒度越大 → 并发性越低 → 冲突概率越高

![image-20260124154513197](C:/Users/Knight/AppData/Roaming/Typora/typora-user-images/image-20260124154513197.png)

InnoDB 是**行级锁**引擎



```mysql
SELECT * FROM user WHERE age = 20 FOR UPDATE;
```

如果 `age` **有索引**

- 锁的是 **age=20 的索引区间**

如果 `age` **没有索引**

- InnoDB 会：
  - 全表扫描
  - **锁住整张表（表锁级别效果，从行锁上升到表锁）**
