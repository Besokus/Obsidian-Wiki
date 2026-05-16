---
type: concept
domain: programming-language
tags: [cpp, mutex, condition-variable, atomic, synchronization]
created: 2026-05-14
updated: 2026-05-14
---

# C++ 线程同步

## 同步方式概览

| 方式 | 作用 | 性能 |
|---|---|---|
| `std::mutex` | 互斥锁，保护临界区 | 中 |
| `std::shared_mutex` | 读写锁，多读一写 | 读并发高 |
| `std::condition_variable` | 线程间事件等待与通知 | 中 |
| `std::atomic` | 无锁原子操作 | 高 |

---

## lock_guard vs unique_lock

| | lock_guard | unique_lock |
|---|---|---|
| 开销 | 小（轻量） | 大（灵活） |
| 手动 lock/unlock | 不支持 | 支持 |
| 延迟加锁 | 不支持 | 支持（defer_lock） |
| 配合 condition_variable | 不支持 | 必须使用 |

**为什么 condition_variable 必须用 unique_lock？**

因为 `wait()` 需要释放锁并在唤醒后重新加锁，`lock_guard` 不支持手动解锁。

---

## 条件变量（condition_variable）

用于线程间等待/通知模式，典型场景是生产者消费者。

### 为什么必须和 mutex 一起用？

核心原因：保证 **"检查条件 + 进入等待"** 的原子性。

没有 mutex 的竞态风险：

```text
线程 A 检查条件不满足，准备 wait
线程 B 修改条件并 notify   ← 信号在此间隙丢失
线程 A 进入 wait，永远等不到通知
```

### 虚假唤醒

线程有时会在没有 `notify` 的情况下被意外唤醒。**必须在 `while` 循环中检查条件**：

```cpp
std::unique_lock<std::mutex> lock(mtx);
cv.wait(lock, []{ return condition; });  // 自带谓词检查
// 等价于：
while (!condition) {
    cv.wait(lock);
}
```

---

## atomic vs mutex

| 维度 | atomic | mutex |
|---|---|---|
| 保护对象 | 单个变量 | 一段临界区 |
| 原子性范围 | 单条操作 | 多条语句 |
| 是否阻塞 | 不阻塞（CPU 指令） | 阻塞（线程睡眠/唤醒） |
| 表达能力 | 弱（简单操作） | 强（复杂逻辑） |
| 性能 | 低竞争快，高竞争抖动 | 稳定但有上下文切换 |

> atomic 解决"单个变量的并发一致性"，mutex 解决"一段代码/多个操作的原子性"。

---

## shared_ptr 线程安全

`shared_ptr` 的**引用计数**操作是原子的（多个线程持不同拷贝是安全的），但**对象本身**的访问不保证线程安全。

多个线程同时修改同一个 `shared_ptr`（如重置、赋值）会发生数据竞争。

`unique_ptr` 不能在多线程中共享。

`sizeof(shared_ptr)`：
- 32 位：8 字节（两个指针）
- 64 位：16 字节（两个指针）

---

## 相关笔记

- [[C++ 线程基础与管理]] — 线程的创建、生命周期、调试
- [[死锁]] — 死锁的条件与避免
