---
type: concept
domain: distributed-systems
topic:
  - distributed-transaction
  - atomic-commit
status: learning
difficulty: hard
mastery: seen
created: 2026-05-16
reviewed:
next_review:
aliases:
  - Two-Phase Commit
  - 2PC
  - 两阶段提交
---

# 2PC（Two-Phase Commit）

## 一句话解释
2PC 是经典的“原子提交协议”：让多个参与者在一次全局事务里 **要么一起提交，要么一起回滚**，从而避免“部分提交”的长期不一致。

> 代价：阻塞与锁持有时间长，分区/协调者故障时可用性差。

---

# 1. 角色与前提

## 1.1 角色
- **Coordinator（协调者 / TM）**：驱动流程、做最终决策。
- **Participants（参与者 / RM）**：持有资源（数据库/服务），执行本地事务并投票。

## 1.2 关键前提（为什么会“卡住”）
分布式网络里：**超时不等于失败**。协调者收不到响应时无法判断“参与者没收到 / 处理慢 / 成功但回包丢了”。

---

# 2. 两个阶段（必须会画时序）

## Phase 1：Prepare / Vote
协调者询问所有参与者：
```text
CanCommit?
```
参与者执行“准备提交”（通常会写入日志 + 锁住资源），并返回：
```text
YES / NO
```

## Phase 2：Commit / Rollback
- 全员 YES → 协调者广播 `COMMIT`
- 任何 NO/超时 → 协调者广播 `ROLLBACK`

---

# 3. 为什么会阻塞（2PC 的核心缺陷）

典型阻塞场景（最重要）：

```text
Phase1：所有参与者 YES 并锁住资源（进入 PREPARED）
协调者在发出 Phase2 决策前宕机/分区
参与者不知道最终是 COMMIT 还是 ROLLBACK
=> 为了 Safety，只能阻塞等待协调者恢复
```

阻塞带来的直接后果：
- 锁时间长（吞吐下降、尾延迟上升）
- 分区时可用性差（业务链路卡死）
- 恢复复杂（依赖协调者日志/决策恢复）

---

# 4. 2PC 什么时候还能用（工程判断）

更合适：
- **短事务**
- 参与者同构（例如多个 DB 分片/XA 资源管理器）
- 能接受“牺牲可用性换强原子”
- 有完备的协调者持久化日志与恢复流程

不合适：
- 微服务长流程（锁太久）
- 外部系统参与（支付/短信等不可控、不确定性强）
- 分区/故障下必须高可用的链路

---

## 相关笔记
- 总览：[[分布式事务（入门整理）]]
- 工程底座：[[幂等性]]、[[事务状态机]]、[[对账]]
- 取舍语义：[[一致性模型]]、[[CAP]]

