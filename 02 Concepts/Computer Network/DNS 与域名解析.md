---
type: concept
domain: computer-network
tags: [dns, domain, resolution]
created: 2026-05-14
updated: 2026-05-14
---

# DNS 与域名解析

## DNS 的作用

将人类可读的域名（如 `example.com`）转换为机器可读的 IP 地址。

## 查询方式

### 迭代查询

```text
客户端 → 本地 DNS 服务器
  → 根 DNS 服务器（告知顶级 DNS 地址）
  → 顶级 DNS 服务器（告知权威 DNS 地址）
  → 权威 DNS 服务器（返回 IP）
  → 缓存结果并返回给客户端
```

本地 DNS 服务器依次询问各级服务器，逐级获得下一级地址。

### 递归查询

与迭代的区别：根 DNS 服务器去查询顶级 DNS 服务器，顶级再查权威，结果逐级返回。

## 缓存

各级 DNS 服务器和客户端都会缓存解析结果，减少重复查询。

---

## 相关笔记

- [[HTTP 与 HTTPS]] — HTTP 请求前先进行 DNS 解析
- [[TCP 协议]] — TCP 连接建立在 IP 地址之上
