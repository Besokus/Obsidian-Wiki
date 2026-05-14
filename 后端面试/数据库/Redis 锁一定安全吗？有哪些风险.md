---
tags: [database]
source: "[[后端岗位面试题]]"
---

# Redis 锁一定安全吗？有哪些风险？

1. 主从切换（Failover）导致锁丢失
2. 锁过期导致并发进入
3. 解锁误删
