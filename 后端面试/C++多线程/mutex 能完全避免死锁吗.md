---
tags: [concurrency]
source: "[[后端岗位面试题]]"
---

# mutex 能完全避免死锁吗

不能。mutex 只能保证互斥访问，**如果多个锁的加锁顺序不一致，仍然可能发生死锁**。
