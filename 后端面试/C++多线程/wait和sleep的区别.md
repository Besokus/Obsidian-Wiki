---
tags: [concurrency]
source: "[[后端岗位面试题]]"
---

# 🌟wait和sleep的区别

**wait**：`wait()`是 Object 类的方法，用于线程间通信，必须在同步块 / 同步方法中调用，调用后会释放持有的锁，可被其他线程调用`notify()`/`notifyAll()`唤醒

**sleep**：`sleep()`是 **Thread** 类的方法，仅让线程暂停执行，**不会释放锁**，超时自动唤醒
