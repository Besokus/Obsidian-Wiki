---
tags: [concurrency]
source: "[[后端岗位面试题]]"
---

# 🌟shared_ptr是线程安全的吗？Sizeof(shared_ptr)是多少（32位机器）

**智能指针不是完全线程安全的。**

以 `shared_ptr` 为例，它的引用计数操作是原子的，所以多个线程同时持有不同的拷贝是安全的；

但如果多个线程**同时修改**同一个 `shared_ptr` 对象，比如重置或赋值，就会发生数据竞争，需要加锁或用 `std::atomic<std::shared_ptr>`。

而 `unique_ptr` 由于独占所有权，不能在多线程中共享。

**总结一句话（面试口头版）**：

shared_ptr 的引用计数操作线程安全，但对象本身访问不保证线程安全。（本身安全，不保证管理对象安全）

unique_ptr 是线程不安全的，不能被多个线程进行拷贝或共享



`shared_ptr` 内部通常包含 **两个指针**：

1. **指向托管对象的指针**
2. **指向 control block 的指针**（存储引用计数等）

**32 位机器**：每个指针 4 字节 → `sizeof(shared_ptr) = 8` 字节

**64 位机器**：每个指针 8 字节 → `sizeof(shared_ptr) = 16` 字节
