# 我们如何在生产中将 AWS elastic cache(redis)集群迁移到另一个集群

> 原文：<https://dev.to/afilmycode/how-we-migrated-aws-elasticache-redis-cluster-to-another-in-production-297f>

我会保持简单。在我目前的组织中，我们有一个关键服务，它使用后端的 redis 作为数据库(而不是缓存)。这一点非常重要，我们承受不起任何停机时间。这将始终获得并发请求(读/写)。到该服务的业务可以来自 500 个写请求。每秒到 2000 次写请求。/秒(24*7)。就读取而言，流量可以轻松超过 1000–1500 req。/秒(24*7)。

我们使用的 redis 是*AWS elastic cache*。当这项服务在 2017 年底推出时，我们分配给它的 redis-cluster 有 **2 个碎片，总共 8 个节点(每个碎片 4 个)。每个节点的实例类型是 m4.xlarge** 。

最近，我们在所有服务中进行了许多成本优化。比如从 Opsworks EC2 infra 迁移到 aws ECS。因此，我们也不得不降低 redis 集群的评级。

所谓降级，我是指降级实例类型(我们选择了 m5.large)并减少每个分片中的节点。

现在 Aws Elasticache 的问题是没有这样直接的方法来做到这一点。Aws Elasticache 有自己的一套限制，例如:

1.  Aws 在其环境中限制了几个 Redis 命令:[https://docs . AWS . Amazon . com/amazonelastache/latest/red-ug/restricted commands . html](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/RestrictedCommands.html)
2.  您不能在多个区域部署 redis 集群，尽管您可以在同一个区域内的多个 AZ(可用性区域)中保存它。

> *对于我们来说，将数据从一个集群迁移到另一个集群的直接方法是拍摄旧 redis 集群的快照，并使用拍摄的快照重新创建新集群。这显然不是一个选项，因为在我们的例子中，当我们拍摄快照并重新创建一个新群集并将流量转移到该群集时，将会有多个写入调用传入现有群集。这将导致巨大的不一致数据。*

在考虑了将数据从一个 redis 集群迁移到另一个 redis 集群并将流量转移到该集群的不同方法，以及零宕机和不一致性的保证之后，我们找到了一个解决方案。

1.)我创建了一个新的 elasticache redis 集群( **2 个分片，4 个 m5.large 类型的节点，即每个分片 2 个**)。
2。)在我们的服务中，有 4 个 API，其中一个是 GET API(最终从 redis 中读取)。剩余的 3 个 API 读写 redis(创建/更新/覆盖值)。所以我修改了代码，这样，每当我们写入现有的 redis 时，在这之后，我们也写入新的 redis。请记住，写入新 redis 时发生的任何错误都不应该影响现有流，也不应该增加延迟。为此，我使用 goroutines(因为我们的服务在 Golang 中)来调用具有写入新 redis 的逻辑的方法。

> 在 update/overwrite 调用的情况下，该错误是意料之中的，因为新的 redis 集群中没有任何键。所以，在这种情况下，我只是记录它。

3.)在部署了上述更改并保持稳定之后，我使用了一个 python 脚本，该脚本将扫描现有 redis 集群中的一个碎片中的现有密钥，并将其复制(基本上是转储和恢复)到新集群的一个碎片中(参见下面的脚本代码和解释)。这也将覆盖新 redis 中的任何现有关键数据(如果由于第 2 步而存在)。我不得不为两个碎片跑一趟

4.)在将所有数据移动到新的 redis 集群之后，我再次运行了另一个 python 脚本，该脚本基本上比较了两个 redis 中每个键的值(逐个分片)。

> 在第 3 步之后，由于写操作已经进入两个 redis，因此成功地协调了数据，除了一个“modified_time”之外，没有任何差异。我会把这个留给你来找出原因。

5.)现在，由于 GET API 仍然从现有的 redis 中读取，我添加了一种 killswitch 逻辑，当启用时，它将从新的 redis 中读取，否则只从现有的(旧的)redis 中读取。基本上，killswitch 只不过是配置中的一个布尔标志。所以，代码看起来像这样:

```
 killswitch_flag = getconfig.getKey(killswitch_key)
       if killswitch_flag is True: 
            GetNewRedisClient.HGETALL(key)
       else :
            GetRedisClient.HGETALL(key) 
```

> 我们在配置中将该标志部署为 TRUE。我们对这一切进行了几天的监控，等待客户对不一致的数据、
> 甚至延迟问题的投诉。幸运的是，我们没有这些。

6.)所以最后，我所要做的就是删除我为写入新 redis 和 killswitch 逻辑而添加的任何额外代码。与此同时，我用我们创建的新 redis 集群端点(在配置中)替换了主 redis 端点。

最后，我们得到的是同样的服务，一个新的 redis 群集，但数据相同。

#### 用于将密钥迁移到新 redis-cluster 的脚本