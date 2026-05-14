---
tags: [concurrency]
source: "[[后端岗位面试题]]"
---

# C++中lock_guard和unique_lock的区别

**lock_guard（轻量、简单）**

> `lock_guard` 是一个轻量级封装，在构造时加锁，在析构时自动解锁，整个生命周期内不能手动解锁，也不能重新加锁。

👉 特点：

- 开销小
- 不支持手动控制



**unique_lock（灵活、功能多）**

> `unique_lock` 功能更丰富，支持**延迟加锁、手动加锁和解锁**、以及**锁的转移（move）**，因此更灵活。

👉 特点：

- 可以：
  - `lock()` / `unlock()`
  - `try_lock()`
  - 延迟加锁（defer_lock）
- 支持与 `condition_variable` 配合使用



**为什么 condition_variable 必须用 unique_lock？**

因为 `condition_variable` 在 wait 时需要**释放锁并在唤醒后重新加锁**，而 `lock_guard` 不支持手动解锁，所以必须使用 `unique_lock`。
