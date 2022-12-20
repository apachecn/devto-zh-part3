# GraphQL API 初学者指南

> 原文：<https://dev.to/marionschleifer/beginners-guide-to-graphql-api-8ke>

今天，我想向您展示如何使用 Hasura GraphQL API 构建无服务器后端。在了解了 GraphQL 及其优势之后，我们将学习如何为我们的 API 创建新表。接下来，我们希望用真实数据填充数据库。当然，我们希望我们的表之间有关系。最后，我们将学习如何使用突变来操纵数据。学习本教程不需要了解 GraphQL。

#### 目录

*   [关于图形 SQL](https://medium.com/p/d5ec68c69f69#b95a)
*   [关于哈苏拉](https://medium.com/p/d5ec68c69f69#8d2f)
*   [创建项目](https://medium.com/p/d5ec68c69f69#68aa)
*   [创建表格](https://medium.com/p/d5ec68c69f69#a0f0)
*   [插入数据](https://medium.com/p/d5ec68c69f69#7ffe)
*   [查询](https://medium.com/p/d5ec68c69f69#55cb)
*   [关系](https://medium.com/p/d5ec68c69f69#bd0c)
*   [对象关系](https://medium.com/p/d5ec68c69f69#da90)
*   [数组关系](https://medium.com/p/d5ec68c69f69#ae14)
*   [多对多关系](https://medium.com/p/d5ec68c69f69#905a)
*   [突变](https://medium.com/p/d5ec68c69f69#857c)
*   [加入社区](https://medium.com/p/d5ec68c69f69#c25c)

### 关于图形 SQL

GraphQL 是一种用于 API 的类型化查询语言。越来越多的科技公司，包括脸书、Twitter 和 GitHub 等科技巨头，正在从通用 REST apis 转向 GraphQL 解决方案。与 REST 等其他 API 架构相比，GraphQL 的主要优势在于:

*   通过简单易懂的查询获得您需要的准确数据
*   获得可靠的结果和有用的错误消息
*   因为数据是直接访问的，而不是通过服务器
*   用一个查询获得几个资源，而不使用几个 URL 和链接资源
*   对所有数据和类型化字段使用一个端点，以实现正确的数据访问
*   将 GraphQL 轻松添加到您现有的应用程序中

以下是一个示例查询: