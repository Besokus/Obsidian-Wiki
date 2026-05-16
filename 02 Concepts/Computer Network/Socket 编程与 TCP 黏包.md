---
type: concept
domain: computer-network
tags: [socket, tcp, sticky-packet, half-packet]
created: 2026-05-14
updated: 2026-05-14
---

# Socket 编程与 TCP 黏包

## TCP Socket 编程流程

### 服务器端

```
创建 socket: socket()
绑定地址: bind()
监听连接: listen()
接受连接: accept() → 返回新 socket
数据收发: send() / recv()
关闭: close()
```

### 客户端

```
创建 socket: socket()
连接服务器: connect()
数据收发: send() / recv()
关闭: close()
```

### 核心注意点

- 网络字节序转换（`htons()`、`htonl()`）
- 错误处理与资源释放
- 并发处理（多线程 / 异步 I/O）

---

## TCP 粘包与拆包

### 问题原因

TCP 是**流式协议**，没有消息边界。

**粘包**：多个消息粘在一起，一次 `recv()` 收到多个数据包（如 Nagle 算法合并小包）。  
**拆包**：一个完整消息被拆成多段，一次 `recv()` 只收到一部分（消息超过 MSS）。

### 解决方案

| 方案 | 做法 |
|---|---|
| 固定长度 | 每个包封装成固定长度，不足补 0 |
| 分隔符 | 包末尾加固定分隔符（如 `\r\n`） |
| 长度头部 | 消息 = 头部(长度) + 体 |
| 自定义协议 | 结合长度 + 类型 + 校验 |

---

## 相关笔记

- [[TCP 协议]] — TCP 的可靠传输机制
- [[IO多路复用与epoll]] — 高并发下的 socket 处理
