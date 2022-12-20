# 在 DynamoDB 表上运行快速 SQL

> 原文：<https://dev.to/rocksetcloud/running-fast-sql-on-dynamodb-tables-4p7n>

您是否曾经想要在不影响生产工作负载的情况下，在 [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) 表上运行 SQL 查询？不需要设置 ETL 作业，然后手动监控该作业，这样做不是很好吗？

在这篇博客中，我将讨论 [Rockset](https://rockset.com/) 如何与 DynamoDB 集成，并随着新对象添加到 DynamoDB 表中而不断自动更新集合。我将逐步介绍如何在 Rockset 和 DynamoDB 表之间建立实时集成，并在其上运行毫秒级延迟的 SQL。

### **DynamoDB 积分**

Amazon DynamoDB 是一个键值和文档数据库，其中的键是在创建表时指定的。DynamoDB 支持对一个或多个项目的扫描操作，并使用 [DynamoDB 流](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html)捕获表活动。使用这些功能，Rockset 分两步连续从 DynamoDB 中获取数据:

1.  用户第一次创建基于 DynamoDB 的集合时，Rockset 会对 DynamoDB 表进行一次完整的扫描。

2.  扫描完成后，Rockset 会继续处理 DynamoDB 流，以记录新的或修改过的记录。

为了确保扫描时 Rockset 不会丢失 DynamoDB 表中记录的任何新数据，Rockset 在 Rockset-DynamoDB 连接器中启用了[强一致性扫描](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadConsistency.html)，并且还创建了 DynamoDB 流(如果尚未存在)并记录了现有碎片的序列号。连续处理步骤(上面的步骤 2)从扫描前记录的序列号开始处理 DynamoDB 流。

DynamoDB 表中的主键值用于构造 Rockset 中的`_id`字段，以唯一标识 Rockset 集合中的文档。这确保了对 DynamoDB 表中现有项的更新应用于 Rockset 中的相应文档。

### **将 DynamoDB 连接到 Rockset**

对于这个例子，我使用一个[黑客新闻数据集](https://github.com/HackerNews/API)以编程方式创建了一个 DynamoDB 表[。数据集由网站上每个帖子和评论的数据组成。数据集中的每个字段在这里都被描述为](https://github.com/rockset/recipes/tree/master/rockset-dynamodb)。我已经在我们的食谱库中包含了这个数据集的一个样本。

该表是使用`id`字段作为 DynamoDB 的分区键创建的。此外，我不得不修改数据集，因为 DynamoDB 不接受空字符串值。使用 Rockset，您将在接下来的几个步骤中看到，您不需要执行这样的 ETL 操作，也不需要提供模式定义来创建一个集合并使其可以通过 SQL 立即查询。

**创建岩石集集合**

我将使用 [Rockset Python 客户端](https://pypi.org/project/rockset/)创建一个由 DynamoDB 表支持的集合。要在您的环境中尝试这一点，您需要创建一个[集成](https://docs.rockset.com/integrations/)(一个表示您的 AWS 凭证的对象)并在 DynamoDB 表上设置相关的[权限](https://docs.rockset.com/source-amazon-dynamodb/)，这允许 Rockset 在该表上执行某些读操作。