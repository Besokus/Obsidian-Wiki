---
type: concept
domain: distributed-systems
topic:
  - distributed-transaction
  - tcc
status: learning
difficulty: hard
mastery: seen
created: 2026-05-16
reviewed:
next_review:
aliases:
  - TCC
  - Try-Confirm-Cancel
---

# TCC（Try / Confirm / Cancel）

## 一句话解释
TCC 把“事务提交/回滚”显式变成业务协议：先 **Try 资源预留**，成功后 **Confirm 正式生效**，失败则 **Cancel 释放预留**。

> 适合库存/优惠券/余额等“可预留资源”的强语义链路；代价是侵入业务、实现复杂。

---

# 1. 三个阶段

## Try：资源预留
```text
冻结库存 / 冻结优惠券 / 冻结余额
```
Try 的目标是把资源“占住”，避免并发冲突（超卖、重复扣减）。

## Confirm：确认提交
把冻结转为正式扣减（最终提交）。

## Cancel：取消回滚
释放冻结资源（取消）。

---

# 2. 必须掌握的三个坑（细节决定成败）

## 2.1 幂等（Idempotency）
Confirm/Cancel 必须支持重复调用（重试、恢复重放、消息重复），要求：
```text
重复执行不产生额外副作用
```
常见做法：用 `gid + 状态机` 做去重与状态推进（参见 [[幂等性]]、[[事务状态机]]）。

## 2.2 空回滚（Empty Cancel）
Try 可能根本没成功（甚至请求未到），但 Cancel 已触发。
Cancel 需要能识别“未冻结/已释放”的情况，返回成功且不出错。

## 2.3 悬挂（Hanging / Late Try）
Try 超时触发 Cancel 后，Try 请求后来才到并成功冻结资源 → 造成错误冻结。
常见防护：
```text
Cancel 时记录已取消状态
Try 到达时若发现已取消 => 拒绝/幂等返回
```

---

# 3. 什么时候用（工程判断）
更合适：
- 资源天然可“冻结/预留”（库存、优惠券、余额、名额）
- 需要较强一致语义（宁可不可用也不能错）

不太合适：
- 参与者过多、长链路（实现成本飙升）
- 外部不可控系统（支付/短信/第三方接口）作为 TCC 参与者

---

## 相关笔记
- 对比：[[Saga]]、[[2PC]]
- 总览：[[分布式事务（入门整理）]]
- 底座：[[幂等性]]、[[事务状态机]]、[[对账]]

