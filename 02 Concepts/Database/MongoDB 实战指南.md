---
type: concept
domain: database
tags: [mongodb, nosql, document-database]
created: 2026-05-14
updated: 2026-05-14
---

# MongoDB 在实际开发中的应用场景、高级用法与工程挑战

## 1. 先建立正确认知

MongoDB 是一种**文档型 NoSQL 数据库**。它不是"没有表的 MySQL"，也不是"随便塞 JSON 的数据库"。它的核心特点是：

```text
以文档 Document 为基本存储单位
用 BSON 存储类 JSON 数据
支持灵活 Schema
支持索引、聚合、事务、复制集、分片集群
```

MongoDB 最适合处理：

- 字段结构灵活的数据
- 嵌套对象较多的数据
- 读写量较高的业务数据
- 查询模式相对明确的数据
- 需要快速迭代的数据模型
- 对关系 Join 依赖不强的业务

一句话总结：

> MongoDB 适合存储"对象型、半结构化、变化快、读写高频"的业务数据，但不适合无脑替代关系型数据库。

---

## 2. MongoDB 的核心数据模型

### 2.1 Document 文档

MongoDB 的一条数据叫 Document，类似 JSON：

```json
{
  "_id": "user_1001",
  "name": "Alice",
  "age": 25,
  "tags": ["vip", "active"],
  "address": {
    "city": "Shanghai",
    "street": "Nanjing Road"
  },
  "createdAt": "2026-05-14T10:00:00Z"
}
```

和关系型数据库不同，MongoDB 不要求同一个集合里的所有文档字段完全一致。

例如同一个 `users` 集合中可以存在：

```json
{
  "_id": "user_1001",
  "name": "Alice",
  "phone": "13800000000"
}
```

也可以存在：

```json
{
  "_id": "user_1002",
  "name": "Bob",
  "email": "bob@example.com",
  "profile": {
    "avatar": "xxx.png",
    "bio": "developer"
  }
}
```

这就是 MongoDB 的灵活性来源。

---

### 2.2 Collection 集合

Collection 类似关系型数据库中的表，但约束更弱。

```text
database
  └── collection
        ├── document
        ├── document
        └── document
```

例如：

```text
shop
  ├── users
  ├── products
  ├── orders
  └── comments
```

---

### 2.3 BSON

MongoDB 底层不是直接存 JSON，而是 BSON。BSON 是 Binary JSON，支持更多数据类型，例如：

- ObjectId
- Date
- Decimal128
- Binary
- Int32 / Int64
- Embedded Document
- Array

这意味着 MongoDB 文档可以表达比普通 JSON 更丰富的类型。

---

## 3. MongoDB 在实际开发中的核心应用场景

### 3.1 用户画像 / 用户资料

用户资料经常是字段多、变化快、差异大的数据。

例如：

```json
{
  "_id": "user_1001",
  "name": "Alice",
  "age": 25,
  "gender": "female",
  "tags": ["vip", "sports", "high_value"],
  "preferences": {
    "language": "zh-CN",
    "theme": "dark",
    "notification": {
      "email": true,
      "sms": false,
      "push": true
    }
  },
  "devices": [
    {
      "type": "ios",
      "model": "iPhone",
      "lastLoginAt": "2026-05-14T08:00:00Z"
    }
  ]
}
```

#### 为什么适合 MongoDB？

因为用户画像通常有这些特征：

- 字段经常增加
- 不同用户字段差异大
- 嵌套结构明显
- 经常按用户 ID 查询整份资料
- 不一定需要复杂 Join

在关系型数据库中，用户画像可能需要拆成很多表：

```text
user
user_profile
user_preferences
user_devices
user_tags
user_ext_attributes
```

而 MongoDB 可以把一个用户的大部分信息组织成一份文档。

#### 工程注意

不要把所有用户相关信息都塞到一个文档里。MongoDB 单文档有大小限制，而且过大的文档会影响读写性能。

适合内嵌的数据：

```text
和用户强绑定、经常一起读取、数量有限
```

不适合内嵌的数据：

```text
无限增长、频繁更新、需要单独查询的大数组
```

---

### 3.2 内容管理系统 CMS

MongoDB 很适合文章、页面、配置、活动内容等场景。

例如文章：

```json
{
  "_id": "article_1001",
  "title": "MongoDB 入门",
  "authorId": "user_1001",
  "status": "published",
  "tags": ["database", "nosql"],
  "content": {
    "type": "markdown",
    "body": "# MongoDB ..."
  },
  "seo": {
    "keywords": ["MongoDB", "NoSQL"],
    "description": "MongoDB learning note"
  },
  "publishedAt": "2026-05-14T10:00:00Z"
}
```

#### 适合原因

- 内容结构灵活
- 页面模块可嵌套
- 扩展字段多
- 版本变化频繁
- 不同内容类型字段不完全一致

例如一个 CMS 中可能有：

```text
文章
专题页
活动页
商品落地页
帮助中心页面
```

这些内容字段结构不完全一致，用 MongoDB 会比固定表结构更灵活。

---

### 3.3 商品信息 / SKU 扩展属性

电商商品的属性差异很大。

例如手机：

```json
{
  "_id": "product_1001",
  "category": "phone",
  "name": "Phone A",
  "brand": "Brand X",
  "attributes": {
    "screenSize": "6.7 inch",
    "storage": "256GB",
    "color": "black",
    "battery": "5000mAh"
  }
}
```

衣服：

```json
{
  "_id": "product_2001",
  "category": "clothing",
  "name": "T-Shirt",
  "brand": "Brand Y",
  "attributes": {
    "size": ["S", "M", "L"],
    "material": "cotton",
    "season": "summer"
  }
}
```

如果用关系型数据库，商品扩展属性常见方案是 EAV 模型：

```text
product_id | attr_name | attr_value
```

但 EAV 查询复杂、性能不稳定、类型约束弱。MongoDB 可以更自然地表示不同品类的扩展属性。

#### 工程注意

如果要支持复杂筛选，例如：

```text
品牌 = Apple
价格 3000-5000
内存 = 256GB
颜色 = 黑色
销量排序
```

MongoDB 可以做一部分，但如果检索维度非常复杂，尤其涉及全文搜索、相关性排序、复杂聚合，通常要结合 Elasticsearch / OpenSearch。

---

### 3.4 日志、事件、行为数据

MongoDB 可以存储结构灵活的日志或事件数据。

例如用户行为事件：

```json
{
  "_id": "event_001",
  "userId": "user_1001",
  "eventType": "product_click",
  "productId": "product_2001",
  "page": "home",
  "device": {
    "os": "iOS",
    "appVersion": "1.2.3"
  },
  "timestamp": "2026-05-14T10:00:00Z"
}
```

不同事件类型字段不同：

```json
{
  "_id": "event_002",
  "userId": "user_1001",
  "eventType": "payment_success",
  "orderId": "order_3001",
  "amount": 199.99,
  "timestamp": "2026-05-14T10:01:00Z"
}
```

#### 适合场景

- 用户行为日志
- 审计日志
- 业务事件
- 设备上报数据
- 应用运行日志
- 风控事件

#### 注意

MongoDB 可以存日志，但并不是所有日志场景都应该用 MongoDB。

如果是超大规模日志检索和分析，常见组合是：

```text
Kafka -> Elasticsearch / ClickHouse / Data Warehouse
```

MongoDB 更适合中等规模、需要按业务字段查询的事件数据。

---

### 3.5 配置中心 / 动态配置

MongoDB 适合存储复杂配置文档。

例如：

```json
{
  "_id": "homepage_config",
  "version": 12,
  "modules": [
    {
      "type": "banner",
      "items": [
        {
          "title": "活动 A",
          "image": "a.png",
          "url": "/campaign/a"
        }
      ]
    },
    {
      "type": "recommendation",
      "algorithm": "hot",
      "limit": 20
    }
  ],
  "enabled": true,
  "updatedAt": "2026-05-14T10:00:00Z"
}
```

适合：

- 首页配置
- 活动页配置
- 推荐策略配置
- A/B Test 配置
- 权限策略配置
- 表单配置
- 工作流配置

#### 为什么适合？

因为配置通常是树状结构，且字段经常变化。关系型数据库需要拆很多表，而 MongoDB 可以直接存储配置对象。

---

### 3.6 表单系统 / 低代码平台

动态表单字段不固定，MongoDB 很适合。

例如表单定义：

```json
{
  "_id": "form_1001",
  "name": "入职申请表",
  "fields": [
    {
      "name": "employeeName",
      "type": "text",
      "required": true
    },
    {
      "name": "department",
      "type": "select",
      "options": ["研发", "产品", "设计"]
    },
    {
      "name": "startDate",
      "type": "date"
    }
  ]
}
```

表单提交：

```json
{
  "_id": "submission_2001",
  "formId": "form_1001",
  "values": {
    "employeeName": "Alice",
    "department": "研发",
    "startDate": "2026-06-01"
  },
  "submittedAt": "2026-05-14T10:00:00Z"
}
```

#### 适合原因

- 字段动态
- 表单结构经常变化
- 不同表单提交结构不同
- 保存整个提交对象更自然

---

### 3.7 物联网 IoT 数据

设备上报数据往往结构不完全一致。

例如：

```json
{
  "_id": "report_1001",
  "deviceId": "device_001",
  "type": "temperature_sensor",
  "metrics": {
    "temperature": 26.5,
    "humidity": 60
  },
  "location": {
    "lng": 121.47,
    "lat": 31.23
  },
  "timestamp": "2026-05-14T10:00:00Z"
}
```

另一个设备：

```json
{
  "_id": "report_1002",
  "deviceId": "device_002",
  "type": "vehicle",
  "metrics": {
    "speed": 65,
    "fuel": 70,
    "engineStatus": "normal"
  },
  "timestamp": "2026-05-14T10:00:01Z"
}
```

MongoDB 可以存储这些半结构化上报数据。

但如果是超高频时间序列数据，比如每秒百万级写入、长期聚合分析，可能要考虑专业时序数据库、ClickHouse、Kafka 数据管道等。

---

### 3.8 地理位置应用

MongoDB 支持地理空间索引，例如 `2dsphere`。

适合：

- 附近门店
- 附近的人
- 外卖配送范围
- 打车附近司机
- 地理围栏
- 城市服务网点查询

文档示例：

```json
{
  "_id": "shop_1001",
  "name": "门店 A",
  "location": {
    "type": "Point",
    "coordinates": [121.4737, 31.2304]
  }
}
```

可以查询附近一定距离内的门店。

#### 注意

MongoDB 能做常见地理查询，但复杂路径规划、地图导航、行政区划边界、多边形叠加分析，通常还是需要专业 GIS 系统。

---

### 3.9 聚合分析

MongoDB 的 Aggregation Pipeline 可以对文档做过滤、分组、排序、投影、连接、统计。

例如统计每天订单金额：

```javascript
db.orders.aggregate([
  {
    $match: {
      status: "paid"
    }
  },
  {
    $group: {
      _id: {
        day: {
          $dateToString: {
            format: "%Y-%m-%d",
            date: "$paidAt"
          }
        }
      },
      totalAmount: { $sum: "$amount" },
      orderCount: { $sum: 1 }
    }
  },
  {
    $sort: {
      "_id.day": 1
    }
  }
])
```

适合：

- 简单报表
- 业务后台统计
- 用户行为聚合
- 订单数据分析
- 内容数据统计

但如果是复杂 OLAP、大宽表、多维分析、海量聚合，通常更适合 ClickHouse、BigQuery、Snowflake、Doris 等分析型系统。

---

## 4. MongoDB 的高级用法

### 4.1 嵌入式建模：Embed

MongoDB 建模中最重要的问题是：

```text
应该内嵌，还是引用？
```

内嵌是把子对象直接放在父文档中。

例如订单内嵌商品快照：

```json
{
  "_id": "order_1001",
  "userId": "user_1001",
  "status": "paid",
  "items": [
    {
      "productId": "product_2001",
      "name": "商品 A",
      "price": 99.0,
      "quantity": 2
    }
  ],
  "amount": 198.0
}
```

#### 适合内嵌的场景

- 子数据和父数据强绑定
- 经常一起读取
- 子数据数量有限
- 子数据生命周期依附父数据
- 子数据不需要独立频繁更新

例如：

- 订单中的商品快照
- 用户设置
- 文章 SEO 配置
- 地址对象
- 页面模块配置

#### 不适合内嵌的场景

- 子数组无限增长
- 子数据经常被单独查询
- 子数据被多个父对象共享
- 子数据更新非常频繁
- 父文档会变得非常大

例如：

- 用户的全部订单
- 商品的全部评论
- 文章的全部点赞用户
- 聊天室的全部消息

---

### 4.2 引用式建模：Reference

引用是通过 ID 关联另一个集合。

例如订单引用用户：

```json
{
  "_id": "order_1001",
  "userId": "user_1001",
  "amount": 198.0
}
```

用户单独存：

```json
{
  "_id": "user_1001",
  "name": "Alice"
}
```

#### 适合引用的场景

- 一对多中"多"的数量很大
- 数据需要独立查询
- 数据被多个地方共享
- 数据更新频繁
- 不希望父文档变大

#### 和关系型数据库的区别

MongoDB 可以用 `$lookup` 做类似 Join 的操作，但不要把 MongoDB 当成强 Join 数据库使用。

如果业务大量依赖复杂多表 Join，关系型数据库通常更自然。

---

### 4.3 索引设计

MongoDB 的性能高度依赖索引。

#### 单字段索引

```javascript
db.users.createIndex({ email: 1 })
```

适合按 email 查询用户：

```javascript
db.users.find({ email: "alice@example.com" })
```

---

#### 复合索引

```javascript
db.orders.createIndex({
  userId: 1,
  status: 1,
  createdAt: -1
})
```

适合查询：

```javascript
db.orders.find({
  userId: "user_1001",
  status: "paid"
}).sort({
  createdAt: -1
})
```

#### 复合索引设计原则

常用经验是 ESR 原则：

```text
Equality -> Sort -> Range
等值条件 -> 排序字段 -> 范围条件
```

例如查询：

```javascript
db.orders.find({
  userId: "user_1001",
  status: "paid",
  createdAt: {
    $gte: start,
    $lte: end
  }
}).sort({
  amount: -1
})
```

索引设计要根据实际查询模式调整，而不是看到字段就建索引。

---

#### 多键索引

数组字段可以建索引。

```json
{
  "title": "MongoDB",
  "tags": ["database", "nosql"]
}
```

```javascript
db.articles.createIndex({ tags: 1 })
```

查询：

```javascript
db.articles.find({ tags: "nosql" })
```

注意：数组索引很有用，但数组太大、组合索引复杂时容易造成索引膨胀。

---

#### TTL 索引

TTL 索引可以自动删除过期文档。

```javascript
db.sessions.createIndex(
  { expireAt: 1 },
  { expireAfterSeconds: 0 }
)
```

适合：

- Session
- Token
- 临时验证码
- 临时任务
- 过期日志
- 短期事件数据

文档：

```json
{
  "_id": "token_001",
  "userId": "user_1001",
  "expireAt": "2026-05-14T12:00:00Z"
}
```

到期后 MongoDB 会自动清理。

##### 注意

TTL 删除不是毫秒级精确，它是后台任务周期性清理。不要把 TTL 索引用作高精度定时任务系统。

---

#### 唯一索引

```javascript
db.users.createIndex(
  { email: 1 },
  { unique: true }
)
```

适合：

- 邮箱唯一
- 手机号唯一
- 用户名唯一
- 第三方账号绑定唯一

---

#### 部分索引 Partial Index

只给满足条件的文档建索引：

```javascript
db.orders.createIndex(
  { userId: 1, createdAt: -1 },
  {
    partialFilterExpression: {
      status: "paid"
    }
  }
)
```

适合：

- 只查询某类状态的数据
- 减少索引体积
- 提高热点查询性能

---

#### 稀疏索引 Sparse Index

只索引存在该字段的文档。

```javascript
db.users.createIndex(
  { phone: 1 },
  { sparse: true }
)
```

适合字段不是所有文档都有的情况。

---

### 4.4 Aggregation Pipeline 聚合管道

Aggregation Pipeline 是 MongoDB 的高级核心能力之一。

它类似数据处理流水线：

```text
输入文档
  ↓
$match 过滤
  ↓
$group 分组
  ↓
$sort 排序
  ↓
$project 字段变换
  ↓
输出结果
```

常见阶段：

| 阶段 | 作用 |
|---|---|
| `$match` | 过滤 |
| `$project` | 字段投影、重命名、计算 |
| `$group` | 分组统计 |
| `$sort` | 排序 |
| `$limit` | 限制数量 |
| `$skip` | 跳过 |
| `$lookup` | 跨集合关联 |
| `$unwind` | 展开数组 |
| `$facet` | 多路聚合 |
| `$bucket` | 分桶 |
| `$merge` | 输出到集合 |

---

#### 示例：按用户统计订单金额

```javascript
db.orders.aggregate([
  {
    $match: {
      status: "paid"
    }
  },
  {
    $group: {
      _id: "$userId",
      totalAmount: { $sum: "$amount" },
      orderCount: { $sum: 1 }
    }
  },
  {
    $sort: {
      totalAmount: -1
    }
  },
  {
    $limit: 10
  }
])
```

#### 工程建议

- 能 `$match` 的尽量前置，减少后续数据量。
- 聚合字段尽量有索引支持。
- 大规模聚合不要影响在线业务库。
- 复杂分析任务最好走数仓或 OLAP 系统。

---

### 4.5 `$lookup` 关联查询

MongoDB 支持 `$lookup` 做集合关联。

例如订单关联用户：

```javascript
db.orders.aggregate([
  {
    $match: {
      status: "paid"
    }
  },
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "user"
    }
  }
])
```

#### 适合场景

- 后台管理查询
- 低频关联查询
- 数据量可控的聚合
- 报表类查询

#### 不适合场景

- 高频在线请求大量 Join
- 多层复杂 Join
- 大集合无索引关联
- 强依赖关系模型的业务

如果系统主要查询模式是复杂关联，关系型数据库通常更合适。

---

### 4.6 Change Streams

Change Streams 可以监听集合或数据库的变更事件。

例如监听订单变更：

```javascript
const changeStream = db.collection("orders").watch();

changeStream.on("change", (change) => {
  console.log(change);
});
```

适合：

- 数据同步
- 缓存刷新
- 搜索索引同步
- 审计日志
- 事件驱动架构
- 异步通知

典型架构：

```text
MongoDB Change Streams
  ↓
同步服务
  ↓
Elasticsearch / Redis / Kafka / 下游系统
```

#### 注意

Change Streams 依赖复制集或分片集群的 oplog。  
消费端要处理：

- 断点续传
- 幂等消费
- 重复事件
- 下游失败重试
- oplog 过期风险

---

### 4.7 事务 Transactions

MongoDB 支持多文档事务。

例如：

```javascript
const session = client.startSession();

await session.withTransaction(async () => {
  await db.collection("accounts").updateOne(
    { _id: "a" },
    { $inc: { balance: -100 } },
    { session }
  );

  await db.collection("accounts").updateOne(
    { _id: "b" },
    { $inc: { balance: 100 } },
    { session }
  );
});
```

#### 适合场景

- 少量文档强一致更新
- 账户类状态变更
- 多集合必须一起成功/失败
- 订单和库存的局部一致性

#### 注意

MongoDB 有事务，但不代表你应该用 MongoDB 按关系型数据库方式建模。

过度依赖事务会削弱 MongoDB 的优势：

- 性能下降
- 锁竞争增加
- 分布式事务复杂
- 分片事务代价更高
- 业务模型可能本来就不适合 MongoDB

优先通过合理文档建模让一次业务操作尽量落在单文档内，因为 MongoDB 对单文档更新天然具备原子性。

---

### 4.8 原子更新操作

MongoDB 很多更新操作是原子的，尤其是单文档内。

常用操作符：

| 操作符 | 作用 |
|---|---|
| `$set` | 设置字段 |
| `$unset` | 删除字段 |
| `$inc` | 数值递增 |
| `$push` | 数组追加 |
| `$pull` | 从数组移除 |
| `$addToSet` | 数组去重添加 |
| `$setOnInsert` | upsert 时设置 |
| `$currentDate` | 设置当前时间 |

#### 示例：计数器

```javascript
db.articles.updateOne(
  { _id: "article_1001" },
  { $inc: { viewCount: 1 } }
)
```

#### 示例：数组去重添加

```javascript
db.users.updateOne(
  { _id: "user_1001" },
  { $addToSet: { tags: "vip" } }
)
```

#### 示例：Upsert

```javascript
db.users.updateOne(
  { email: "alice@example.com" },
  {
    $set: {
      name: "Alice",
      updatedAt: new Date()
    },
    $setOnInsert: {
      createdAt: new Date()
    }
  },
  { upsert: true }
)
```

---

### 4.9 分片 Sharding

当单机或单复制集无法承载数据量和吞吐时，可以使用分片集群。

分片架构：

```text
Client
  ↓
mongos 路由
  ↓
config servers
  ↓
shard1 / shard2 / shard3
```

#### 分片键 Shard Key

分片键决定数据如何分布。

例如：

```javascript
sh.shardCollection("shop.orders", { userId: 1 })
```

#### 好的分片键特点

- 基数高
- 分布均匀
- 查询经常带上该字段
- 不会造成单调递增热点
- 支持主要访问模式

#### 差的分片键

例如按自增时间字段：

```javascript
{ createdAt: 1 }
```

如果写入总是最新时间，会导致写入集中到一个分片，形成热点。

#### 分片的挑战

分片不是单纯扩容按钮。它会带来：

- 分片键选择困难
- 数据倾斜
- 跨分片查询变慢
- 跨分片事务复杂
- chunk 迁移成本
- 运维复杂度上升

---

### 4.10 读写关注：Read Concern / Write Concern

MongoDB 允许配置读写一致性级别。

#### Write Concern

控制写入要确认到什么程度才算成功。

例如：

```javascript
{ w: "majority" }
```

表示写入被多数节点确认后才返回成功。

适合对可靠性要求较高的写入。

#### Read Concern

控制读取能看到什么级别的数据。

例如：

```javascript
{ readConcern: { level: "majority" } }
```

表示读取多数节点确认过的数据。

#### 工程权衡

更强一致性通常意味着：

```text
更高可靠性 + 更高延迟
```

更弱一致性通常意味着：

```text
更低延迟 + 更高数据异常风险
```

---

### 4.11 Schema Validation

虽然 MongoDB Schema 灵活，但生产系统不能完全无约束。

可以使用 JSON Schema 做集合级校验。

例如：

```javascript
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email"],
      properties: {
        name: {
          bsonType: "string"
        },
        email: {
          bsonType: "string"
        },
        age: {
          bsonType: "int",
          minimum: 0
        }
      }
    }
  }
})
```

#### 适合场景

- 核心业务集合
- 多团队共用集合
- 防止脏数据
- 约束必要字段
- 限制字段类型

#### 注意

MongoDB 的灵活 Schema 是优势，但无约束滥用会变成灾难。实际开发中应该做到：

```text
业务层 Schema 管理 + 数据库层必要校验 + 版本迁移策略
```

---

### 4.12 Time Series Collection

MongoDB 支持时序集合，适合时间序列数据。

适合：

- 设备监控指标
- 应用性能指标
- 传感器数据
- 价格变化记录
- 状态采样数据

文档通常类似：

```json
{
  "deviceId": "device_001",
  "timestamp": "2026-05-14T10:00:00Z",
  "temperature": 26.5,
  "humidity": 60
}
```

#### 注意

时序集合适合中等规模时序业务。  
如果是大规模监控、日志、指标分析，可能仍需要 Prometheus、VictoriaMetrics、InfluxDB、ClickHouse 等专门系统。

---

## 5. MongoDB 面临的工程挑战

### 5.1 数据建模挑战

MongoDB 最大的挑战不是语法，而是建模。

关系型数据库通常先想：

```text
实体有哪些？
表怎么拆？
外键关系是什么？
如何规范化？
```

MongoDB 更应该先想：

```text
业务怎么查询？
数据是否一起读取？
数据是否一起更新？
数组是否会无限增长？
是否需要事务？
是否会出现热点文档？
```

#### 常见错误

把 MongoDB 当关系型数据库用：

```text
users
orders
order_items
products
categories
addresses
```

然后频繁 `$lookup`，这会让 MongoDB 的优势消失。

另一个错误是把所有东西都内嵌：

```text
一个 user 文档里塞所有订单、评论、消息、浏览记录
```

这会导致文档无限变大、更新冲突、性能下降。

#### 正确原则

```text
一起读、一起改、数量有限 -> 可以内嵌
独立查、独立改、数量巨大 -> 应该引用
```

---

### 5.2 Schema 演进与脏数据

MongoDB 允许灵活字段，但业务长期运行后容易出现：

```json
{ "age": 18 }
{ "age": "18" }
{ "user_age": 18 }
{ "profile": { "age": 18 } }
```

也可能出现：

```json
{ "status": "paid" }
{ "status": 2 }
{ "orderStatus": "PAID" }
```

#### 问题

- 查询逻辑复杂
- 数据分析困难
- 索引命中异常
- 代码兼容成本上升
- 字段语义混乱
- 老数据迁移困难

#### 解决方案

1. 在应用层定义明确 Model。
2. 使用 Schema Validation 约束关键集合。
3. 数据结构变更要有版本号。
4. 写入路径统一治理。
5. 对历史数据做迁移脚本。
6. 避免多个服务随意写同一集合。

#### 推荐做法

文档中加入版本字段：

```json
{
  "_id": "user_1001",
  "schemaVersion": 3,
  "profile": {
    "name": "Alice"
  }
}
```

读取时可以根据版本做兼容，后台逐步迁移旧数据。

---

### 5.3 索引膨胀与索引失效

MongoDB 查询性能高度依赖索引，但索引不是越多越好。

#### 索引过多的问题

- 写入变慢
- 占用更多内存和磁盘
- 更新成本上升
- 查询优化器选择复杂
- 备份和恢复变慢

每次写入文档，相关索引也要更新。

#### 索引不足的问题

- 查询全表扫描
- CPU 飙高
- 磁盘 I/O 增加
- 慢查询增多
- 影响线上性能

#### 常见问题

##### 复合索引顺序错误

查询：

```javascript
db.orders.find({
  userId: "user_1001",
  status: "paid"
}).sort({
  createdAt: -1
})
```

较合适索引：

```javascript
{ userId: 1, status: 1, createdAt: -1 }
```

如果建成：

```javascript
{ createdAt: -1, status: 1, userId: 1 }
```

可能无法很好支持查询。

##### 低基数字段单独建索引效果差

例如：

```javascript
{ gender: 1 }
{ status: 1 }
```

如果大部分文档都是同一个 status，索引选择性很差，收益有限。

##### 数组索引膨胀

数组字段建索引时，一个文档可能产生多个索引项。数组很大时，索引体积会迅速增加。

---

### 5.4 大文档与大数组

MongoDB 单文档有大小限制，而且大文档会带来明显性能问题。

#### 大文档危害

- 读取时传输数据过大
- 更新时移动文档成本高
- 内存占用高
- 网络延迟增加
- 复制压力增加
- 聚合性能下降

#### 大数组危害

例如把用户所有消息塞进一个数组：

```json
{
  "_id": "user_1001",
  "messages": [
    "...",
    "...",
    "几十万条消息"
  ]
}
```

问题：

- 数组无限增长
- 更新冲突严重
- `$push` 频繁修改同一文档
- 查询分页困难
- 文档可能超过大小限制

#### 解决方案

把无限增长数据拆成独立集合：

```text
users
messages
```

或者按时间、业务维度分桶：

```text
user_message_bucket_2026_05
```

---

### 5.5 热点文档问题

MongoDB 单文档更新是原子的，但如果大量请求同时更新同一个文档，会出现热点。

#### 例子

文章阅读数：

```javascript
db.articles.updateOne(
  { _id: "article_1001" },
  { $inc: { viewCount: 1 } }
)
```

如果某篇文章爆火，所有请求都更新同一个文档，会造成写热点。

#### 解决方案

##### 1. 分片计数器

```text
article:1001:counter:0
article:1001:counter:1
article:1001:counter:2
...
```

写入时随机写一个分片，读取时聚合求和。

MongoDB 中可以设计为：

```json
{
  "articleId": "article_1001",
  "bucket": 0,
  "count": 1234
}
```

##### 2. 异步聚合

高频事件先写入队列或日志系统，再异步汇总到 MongoDB。

##### 3. Redis 前置

阅读数、点赞数等高频计数先写 Redis，再定期刷回 MongoDB。

---

### 5.6 写入放大与频繁更新

MongoDB 文档更新可能引发额外开销，尤其是文档变大时。

例如频繁往数组里 `$push`：

```javascript
db.posts.updateOne(
  { _id: "post_1001" },
  { $push: { comments: newComment } }
)
```

如果评论很多，这个文档会越来越大。

#### 工程建议

- 高频增长型数组不要内嵌。
- 高频计数考虑 Redis 或分片计数器。
- 大对象更新尽量局部字段更新，不要整文档覆盖。
- 对冷热数据分离，频繁变动字段单独存。
- 避免多个服务频繁更新同一文档不同字段造成竞争。

---

### 5.7 查询能力边界

MongoDB 查询能力比很多 NoSQL 强，但仍有边界。

#### 不适合的查询模式

- 大量复杂 Join
- 高度规范化数据模型
- 复杂报表
- 多维 OLAP 分析
- 强事务一致性流程
- 复杂全文搜索和相关性排序
- 跨集合复杂聚合

#### 应该考虑其他系统

| 需求 | 更适合系统 |
|---|---|
| 强事务、复杂关系 | PostgreSQL / MySQL |
| 全文搜索 | Elasticsearch / OpenSearch |
| 大规模 OLAP | ClickHouse / BigQuery / Snowflake |
| 高吞吐事件流 | Kafka / Pulsar |
| 缓存和计数 | Redis |
| 图关系查询 | Neo4j / JanusGraph |

---

### 5.8 分片键选择困难

一旦数据量大到需要分片，分片键设计会成为核心挑战。

#### 好分片键

例如按用户维度访问订单：

```javascript
{ userId: 1 }
```

如果大多数查询都带 `userId`，并且用户分布较均匀，这是不错的选择。

#### 坏分片键

##### 1. 单调递增字段

```javascript
{ createdAt: 1 }
```

新写入总是落到最后一个范围，形成写热点。

##### 2. 低基数字段

```javascript
{ status: 1 }
```

状态只有几个值，数据分布不均匀。

##### 3. 查询不用的字段

如果查询经常不带分片键，就会广播到所有分片，性能差。

#### 分片后问题

- 数据分布不均
- 某些 shard 特别热
- 跨 shard 查询慢
- 跨 shard 聚合代价高
- 跨 shard 事务复杂
- 后期换分片键成本高

---

### 5.9 复制延迟与一致性

MongoDB 通常使用复制集保证高可用。

```text
Primary
  ↓
Secondary 1
  ↓
Secondary 2
```

写入通常先到 Primary，再复制到 Secondary。

#### 风险

如果读请求读 Secondary，可能读到旧数据：

```text
写入 Primary 成功
Secondary 还没同步
读 Secondary
读到旧值
```

#### 应对方式

- 关键读走 Primary。
- 使用合适的 read preference。
- 关键写使用 majority write concern。
- 对一致性要求高的流程，不依赖异步副本读。
- 监控 replication lag。

相关笔记：[[主从复制]]

---

### 5.10 事务成本与锁竞争

MongoDB 支持事务，但事务不是免费的。

事务会带来：

- 更长的锁持有时间
- 更高内存开销
- 更复杂的冲突处理
- 更高延迟
- 分片事务成本更高

#### 工程建议

- 优先让一次业务操作落在单文档内。
- 少用大事务、长事务。
- 避免事务中做外部调用。
- 事务内读写文档数量要可控。
- 事务失败要支持重试和幂等。

---

### 5.11 聚合查询影响线上性能

Aggregation Pipeline 很强，但复杂聚合可能非常重。

例如：

- 大量 `$group`
- 大集合 `$sort`
- 无索引 `$match`
- 大范围 `$lookup`
- `$unwind` 展开超大数组
- 跨分片聚合

这些会导致：

- CPU 升高
- 内存压力
- 磁盘临时文件
- 慢查询
- 影响在线请求

#### 建议

- 在线业务库避免跑重分析。
- 大聚合放到离线系统。
- 高频统计提前预聚合。
- 为 `$match` 和 `$sort` 设计索引。
- 使用只读副本或分析库承接报表。

---

### 5.12 运维与可观测性挑战

MongoDB 线上需要关注：

- 慢查询
- 索引命中率
- 工作集是否在内存中
- 连接数
- 锁等待
- 复制延迟
- oplog 大小
- 磁盘使用
- 分片数据分布
- chunk 迁移
- WiredTiger cache
- 页错误和 I/O 延迟

如果只会写 CRUD，不懂这些指标，线上很容易出现"开发环境很快，生产环境很慢"的问题。

---

## 6. MongoDB 与 MySQL / PostgreSQL 的选择

### 6.1 适合 MongoDB 的情况

可以优先考虑 MongoDB：

- 数据结构经常变化
- 文档天然是嵌套对象
- 查询主要围绕单个聚合根
- 需要快速迭代字段
- 不依赖大量复杂 Join
- 单文档读写能覆盖主要业务
- 数据量较大，需要较自然的水平扩展
- 半结构化数据较多

例如：

```text
用户画像
商品扩展属性
内容管理
配置系统
动态表单
事件日志
IoT 数据
地理位置对象
```

---

### 6.2 适合关系型数据库的情况

更适合 MySQL / PostgreSQL：

- 强事务
- 强一致
- 复杂 Join
- 复杂约束
- 数据模型稳定
- 财务、支付、订单核心账本
- 报表需要 SQL 生态
- 数据完整性约束非常重要

例如：

```text
支付流水
账户余额
订单主流程
库存账本
财务对账
ERP 核心数据
权限关系模型
```

---

### 6.3 常见组合架构

实际系统经常不是二选一，而是组合：

```text
MySQL / PostgreSQL：核心交易数据
MongoDB：灵活业务文档、配置、内容、画像
Redis：缓存、限流、计数、锁
Elasticsearch：搜索
Kafka：事件流
ClickHouse：分析报表
```

关键是让每个系统做自己擅长的事情。

---

## 7. MongoDB 实际开发中的 Key / Schema 设计建议

### 7.1 以查询模式设计文档

不要先问"有哪些实体"，而要问：

```text
页面需要一次性读取什么？
接口最常按什么条件查？
哪些字段一起更新？
哪些数据会无限增长？
哪些字段需要索引？
```

MongoDB 建模是 query-driven design。

---

### 7.2 控制文档大小

经验上要避免：

- 一个文档无限增长
- 一个数组无限增长
- 一个文档包含大量不常用字段
- 一个请求只需要 3 个字段却读出巨大文档

可以通过字段投影减少读取：

```javascript
db.users.find(
  { _id: "user_1001" },
  { name: 1, email: 1 }
)
```

---

### 7.3 区分读模型和写模型

有些场景可以为查询冗余数据。

例如订单中保存商品快照：

```json
{
  "productId": "product_1001",
  "productName": "商品 A",
  "priceAtOrderTime": 99.0
}
```

即使商品名称后来改了，订单仍然保留下单时的商品信息。

这不是坏设计，而是符合业务语义的冗余。

---

### 7.4 设计字段版本

为了应对 Schema 演进，可以加：

```json
{
  "schemaVersion": 2
}
```

这样应用可以兼容旧文档，后台逐步迁移。

---

### 7.5 避免无控制的动态字段

例如：

```json
{
  "attr_color": "black",
  "attr_size": "XL",
  "attr_material": "cotton"
}
```

如果动态字段无限扩散，会导致：

- 索引无法管理
- 查询复杂
- 字段命名混乱
- 数据治理困难

更好的方式是统一放到 `attributes` 下：

```json
{
  "attributes": {
    "color": "black",
    "size": "XL",
    "material": "cotton"
  }
}
```

或者对关键字段单独提升为顶层字段并建索引。

---

## 8. 典型案例：商品系统如何使用 MongoDB

### 8.1 商品文档

```json
{
  "_id": "product_1001",
  "categoryId": "phone",
  "name": "Phone A",
  "brand": "Brand X",
  "price": 3999,
  "status": "online",
  "attributes": {
    "screenSize": "6.7",
    "storage": "256GB",
    "color": "black"
  },
  "images": [
    "a.png",
    "b.png"
  ],
  "createdAt": "2026-05-14T10:00:00Z",
  "updatedAt": "2026-05-14T10:00:00Z"
}
```

### 8.2 适合 MongoDB 的部分

- 商品扩展属性
- 商品详情页数据
- 商品图片、描述、配置
- 不同类目不同字段
- 商家后台快速迭代字段

### 8.3 不一定适合 MongoDB 的部分

- 库存最终账本
- 订单支付流程
- 财务结算
- 复杂搜索筛选
- 高并发库存扣减

这些可能分别适合：

```text
库存账本 -> MySQL / PostgreSQL
搜索筛选 -> Elasticsearch / OpenSearch
缓存加速 -> Redis
订单交易 -> MySQL / PostgreSQL
```

### 8.4 查询索引设计

常见查询：

```javascript
db.products.find({
  categoryId: "phone",
  status: "online"
}).sort({
  createdAt: -1
})
```

索引：

```javascript
db.products.createIndex({
  categoryId: 1,
  status: 1,
  createdAt: -1
})
```

按品牌和价格筛选：

```javascript
db.products.find({
  categoryId: "phone",
  brand: "Brand X",
  price: {
    $gte: 3000,
    $lte: 5000
  }
})
```

索引可能是：

```javascript
db.products.createIndex({
  categoryId: 1,
  brand: 1,
  price: 1
})
```

但如果筛选条件非常多，靠 MongoDB 全部解决可能会变复杂，需要搜索引擎辅助。

---

## 9. 典型案例：评论系统如何使用 MongoDB

### 9.1 错误设计：评论全部内嵌文章

```json
{
  "_id": "article_1001",
  "title": "MongoDB",
  "comments": [
    {
      "userId": "u1",
      "content": "good"
    },
    {
      "userId": "u2",
      "content": "nice"
    }
  ]
}
```

如果评论越来越多，这个文档会越来越大，最终出现性能问题。

### 9.2 更合理设计：评论独立集合

```json
{
  "_id": "comment_1001",
  "articleId": "article_1001",
  "userId": "user_1001",
  "content": "good",
  "status": "visible",
  "createdAt": "2026-05-14T10:00:00Z"
}
```

索引：

```javascript
db.comments.createIndex({
  articleId: 1,
  createdAt: -1
})
```

查询文章评论：

```javascript
db.comments.find({
  articleId: "article_1001",
  status: "visible"
}).sort({
  createdAt: -1
}).limit(20)
```

这比大数组内嵌更可扩展。

---

## 10. 常见误区

### 误区一：MongoDB 不需要 Schema

MongoDB 是灵活 Schema，不是没有 Schema。

正确理解：

```text
Schema 从数据库表结构转移到应用层、校验规则和数据治理流程中
```

如果完全不管 Schema，后期会出现大量脏数据。

---

### 误区二：MongoDB 不支持事务，所以不可靠

MongoDB 支持事务。但它的最佳实践仍然是：

```text
通过文档模型减少事务需求
```

不是所有系统都应该用事务，也不是所有事务场景都适合 MongoDB。

---

### 误区三：MongoDB 可以替代 MySQL

MongoDB 可以替代一部分关系型数据库场景，但不是所有。

如果业务强依赖：

- Join
- 外键
- 强一致
- 复杂事务
- SQL 报表

关系型数据库通常更合适。

---

### 误区四：字段灵活就可以随便写

字段灵活是为了支持业务变化，不是为了放弃治理。

生产环境必须考虑：

- 字段命名规范
- 类型一致性
- 必填字段
- 索引设计
- 版本迁移
- 数据质量检查

---

### 误区五：索引越多越好

索引会提升查询，但会拖慢写入，并占用大量空间。  
索引应该由真实查询驱动，而不是看到字段就建。

---

## 11. MongoDB 使用决策框架

当你考虑使用 MongoDB 时，可以问这些问题：

1. 数据是否天然是对象或文档结构？
2. 字段是否经常变化？
3. 是否经常一次读取整份对象？
4. 是否可以接受一定程度的冗余？
5. 是否不依赖大量复杂 Join？
6. 是否主要按少数明确条件查询？
7. 是否能设计合理索引？
8. 是否能控制文档大小和数组增长？
9. 是否有 Schema 版本和迁移策略？
10. 是否明确哪些数据不该放 MongoDB？

如果答案偏向：

```text
文档型数据 + 灵活字段 + 查询模式明确 + 关系不复杂
```

MongoDB 很合适。

如果答案偏向：

```text
强事务 + 复杂关系 + 高度规范化 + 复杂 SQL 分析
```

应优先考虑关系型数据库或专门分析系统。

---

## 12. 总结

MongoDB 在实际开发中的主要价值是：

```text
用文档模型高效表达复杂对象和半结构化数据，并支持灵活演进
```

它适合：

- 用户画像
- 内容管理
- 商品扩展属性
- 配置中心
- 动态表单
- 业务事件
- IoT 数据
- 地理位置对象
- 中等规模聚合分析
- 快速迭代型业务数据

它的高级能力包括：

- 嵌入式建模
- 引用式建模
- 复合索引
- 多键索引
- TTL 索引
- 部分索引
- 聚合管道
- `$lookup`
- Change Streams
- 多文档事务
- 原子更新
- 分片集群
- Read / Write Concern
- Schema Validation
- Time Series Collection

它面临的工程挑战包括：

- 数据建模难
- Schema 演进难
- 脏数据治理难
- 索引设计复杂
- 大文档和大数组问题
- 热点文档
- 写入放大
- 查询能力边界
- 分片键选择困难
- 复制延迟
- 事务成本
- 聚合查询影响线上性能
- 运维和监控复杂度

真正会用 MongoDB，不是会写 `find` 和 `insert`，而是能判断：

> 哪些数据应该内嵌，哪些数据应该引用；哪些查询应该建索引，哪些查询应该交给搜索或数仓；哪些业务适合文档模型，哪些业务仍然应该使用关系型数据库。

---

## 相关笔记

- [[Redis 实战指南]] — 缓存、计数、限流等场景与 MongoDB 配合使用
- [[主从复制]] — 数据库复制的基本原理，MongoDB 复制集也遵循类似模式
