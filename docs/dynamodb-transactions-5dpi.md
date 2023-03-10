# DynamoDB 事务

> 原文：<https://dev.to/napicella/dynamodb-transactions-5dpi>

你有一个想法，你想尝试一下？很好，将代码打包并推送给你最喜欢的云提供商，你的应用程序将立即可供成千上万甚至上百万的客户使用。

哇，这是成为开发者的大好时机。

从现在起，更好的是 AWS 刚刚在 Re:invent 宣布了 Dynamo DB 交易！

### 为什么有关系？

#### 原子性

它支持对多个项目进行原子操作，要么全部发生，要么什么都不发生。

#### 隔离

这叫做与数据库人员的隔离，这叫做与分布式系统人员的一致性。所以在这种情况下，这些词是可以互换的。当项目在事务之外被修改时，Dynamo 事务通过拒绝事务来提供顺序一致性。

查看我关于[一致性模型](https://dev.to/napicellatwit/consistency-models-52)的旧文章，了解更多关于顺序一致性的含义

### 资源

1.  [Aws 关于迪纳摩交易的博文](https://aws.amazon.com/blogs/aws/new-amazon-dynamodb-transactions/)
2.  [在重新发明期间遵循 AWS 公告的完整列表](https://aws.amazon.com/new/reinvent/)