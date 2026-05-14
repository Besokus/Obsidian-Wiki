---
tags: [moc, concurrency]
---

# C++ 多线程 MOC

## 笔记列表

```dataview
LIST FROM "后端面试/C++多线程" WHERE !contains(tags, "moc") SORT file.name ASC
```

## 核心问题速查

| 问题 | 笔记 |
|------|------|
| 创建和管理线程 | [[C++中创建和管理线程（RAII）]] |
| 线程安全 | [[什么是线程安全]] |
| 多线程问题 | [[多线程会带来哪些问题]] |
| mutex 与死锁 | [[mutex 能完全避免死锁吗]] |
| 锁的粒度 | [[锁的粒度]] |
| 多线程同步方式 | [[多线程同步方式]] |
| 条件变量 | [[条件变量condition_variable]] |
| lock_guard vs unique_lock | [[C++中lock_guard和unique_lock的区别]] |
| 线程死锁 | [[线程死锁的原因，如何避免死锁]] |
| wait vs sleep | [[wait和sleep的区别]] |
| 条件变量为什么需要 mutex | [[为什么条件变量必须和 mutex 一起使用？]] |
| 线程崩溃 | [[一个线程崩溃会发生什么？]] |
| shared_ptr 线程安全 | [[shared_ptr是线程安全的吗？Sizeof(shared_ptr)是多少（32位机器）]] |
| 多线程调试 | [[多线程中如果某个线程出现问题，应该如何调试]] |
| atomic vs mutex | [[原子操作atomic和mutex的使用场景和区别]] |
