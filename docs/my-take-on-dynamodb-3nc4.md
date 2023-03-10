# 我对 dynamodb 的看法

> 原文：<https://dev.to/kyleparisi/my-take-on-dynamodb-3nc4>

# 为什么是 Dynamodb

每次我看 Dynamodb SDK 文档时，我都会想‘伙计，这是一个过度工程化的烂摊子’。使用 dynamodb 的显而易见的原因是，它是托管的，内置了自动伸缩功能，而且很便宜。技术思路是计算(即 mysql)比映射存储(即 dynamodb)贵得多。

# 为什么我会使用它

使用它作为键值存储`1:1`，或者时间序列数据源`1:N`，都是清晰且易于实现的。我认为这可能是你应该考虑使用它的全部原因。如果您需要`N:M`，您需要添加一个“全局二级键”,它类似于您的主表的条目的更高级克隆，不要求分区键的唯一性。如果需要`N:M:O`，就用关系数据库。

# 外带阅读数据

### 扫描

[![scan abilities](img/3d0dde7493d2eba349ba1bb436164b71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zr0D6CM6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v3pjb7gt5d9pvdmfvti2.png)

当您不知道分区键时，请使用扫描。出于“数据科学”的原因，您可能希望保留该操作。扫描将遍历*所有*的数据。如果你没有很多数据(< 10GB)，做你想做的。

### 查询

[![query abilities](img/5dd9ccdbd58873296ade9daf85314179.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VKoQzgD_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9i8xiq8knge3cavjcx98.png)

当您知道分区键并希望按排序键排序时，请使用查询。

### 滤镜

[![filter abilities](img/73348a4da85050fe5da1d2b5d6ff6a34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1v8CUw2r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9rxz0o95gqf22k75zx5f.png)

过滤器可以应用于扫描或查询。它们发生在扫描或查询完成的之后的*。查询和筛选器可以是强大的组合，例如按品牌或价格筛选产品列表。*

### 获取物品

有趣的是，AWS 控制台没有获取项目选项。这可能是因为一个查询就可以解决这个操作。使用这种方法显然会返回 1 条记录，并且在所有方面都是最有效的。

# 创建、更新、删除

这些都是原子级的普通操作。如果您需要批处理一个操作，SDK 中有一些方法。

# 我喜欢的一个用例

我认为用户会话是`1:1`或`1:N`用例的有趣候选。静态数据将自动加密。Dynamodb 表也可以配置为对项目使用 TTL。在`1:N`场景中，如果发生注销操作，并且您将排序键设置为包含用户 id，那么您可以一次删除所有会话。