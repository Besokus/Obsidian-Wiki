---
type: concept
domain: computer-network
tags: [url, http, dns, tcp, browser]
created: 2026-05-14
updated: 2026-05-14
---

# 输入 URL 后发生了什么

## 完整流程

```text
① DNS 解析（域名 → IP）
② TCP 三次握手（建立连接）
③ TLS 握手（如果是 HTTPS）
④ 发送 HTTP 请求
⑤ 服务器处理并返回响应
⑥ 浏览器渲染页面
⑦ 断开 TCP 连接（四次挥手）
```

## 各步骤详解

### ① DNS 解析

浏览器缓存 → 本地 DNS 缓存 → 本地 DNS 服务器 → 根 DNS → 顶级 DNS → 权威 DNS，最终获取 IP。

### ② TCP 三次握手

客户端与服务端建立可靠的传输连接（详见 [[TCP 协议]]）。

### ③ TLS 握手（HTTPS）

验证服务器证书、协商对称密钥，建立加密通道（详见 [[HTTP 与 HTTPS]]）。

### ④ 发送 HTTP 请求

浏览器构造 HTTP 请求报文（Method、URL、Headers、Body），通过 TCP 连接发送。

### ⑤ 服务器处理

服务器接收请求、路由处理、查询数据、构造响应并返回。

### ⑥ 浏览器渲染

解析 HTML → 构建 DOM 树 → 解析 CSS → 渲染 → 布局 → 绘制。

### ⑦ 断开连接

TCP 四次挥手，释放连接资源。

---

## 相关笔记

- [[DNS 与域名解析]] — 第一步 DNS 解析原理
- [[TCP 协议]] — 连接建立和断开的完整机制
- [[HTTP 与 HTTPS]] — 请求/响应协议细节
