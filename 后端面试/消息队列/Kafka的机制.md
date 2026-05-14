---
tags: [mq]
source: "[[后端岗位面试题]]"
---

# Kafka的机制

Kafka 的消费者模型主要包含三个核心机制：**Pull 拉取、分区分配、Offset 管理**。

第一，Kafka 采用 Pull 模型，消费者通过 `poll()` 主动从 Broker 拉取消息，而不是由服务端推送，这样可以更好地控制消费速率，并支持批量拉取提升吞吐。

第二，多个消费者会组成一个 Consumer Group。Kafka 会在消费者加入或退出时触发 Rebalance，将 Topic 的多个 Partition 分配给不同的消费者。一个 Partition 在同一个 Consumer Group 内只会分配给一个消费者，从而保证分区内的消息顺序。

第三，Kafka 使用 Offset 来记录消费进度。消费者在处理完消息后再提交 offset，这样即使发生故障，也可以从上次位置继续消费。为了避免重复消费带来的问题，业务层通常需要做幂等处理。

整体来说，Kafka 是通过 Partition 提供并发能力，通过 Consumer Group 实现负载均衡，通过 Offset 保证消费进度和容错恢复。
