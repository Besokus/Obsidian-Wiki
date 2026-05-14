---
tags: [network]
source: "[[后端岗位面试题]]"
---

# 🌟Http请求中有哪些请求方式

HTTP 常见的请求方式包括 **GET、POST、PUT、PATCH、DELETE**、HEAD、OPTIONS。
 GET 用于获取资源，POST 用于**提交或创建资源**，PUT/PATCH 用于**更新资源**，DELETE 用于删除资源。
 其中 **GET、PUT、DELETE 是幂等的，POST是不幂等的**，GET、HEAD、OPTIONS 是安全的。
 一般实际项目中 RESTful 风格接口会根据资源操作语义选择不同请求方法。



**GET和POST的主要区别**

GET用于从服务器**获取数据**，将数据附加在**URL**上发送，对数据长度有限制，不安全，可被缓存;

POST用于向服务器**提交数据**，将数据放在**请求体**中发送，数据长度没有限制，相对安全，不可被缓存。
