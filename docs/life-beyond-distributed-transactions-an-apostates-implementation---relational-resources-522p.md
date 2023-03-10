# 分布式事务之外的生活:叛教者的实现——关系资源

> 原文：<https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---relational-resources-522p>

此系列中的帖子:

*   [一个引子](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---a-primer-2po6-temp-slug-8261948)
*   [文件协调](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-coordination-leb-temp-slug-1009285)
*   [文档示例](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---document-example-4fpk-temp-slug-5917155)
*   [调度示例](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---dispatching-example-31i6-temp-slug-684211)
*   [失败和重试](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---failures-and-retries-m6-temp-slug-2730961)
*   [故障恢复](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---dispatcher-failure-recovery-3m38-temp-slug-5793122)
*   [传奇故事](https://dev.to/jbogard/life-beyond-distributed-transactions-an-apostates-implementation---sagas-2dj4-temp-slug-1869324)
*   [关系资源](https://jimmybogard.com/life-beyond-distributed-transactions-an-apostates-implementation-relational-resources/)

[本系列的示例代码](https://github.com/jbogard/adventureworkscosmos)

到目前为止，在本系列中，我们主要关注的是不能支持分布式(或多实体)事务的单个资源。虽然作为 NoSQL 选项，这变得越来越不常见，但由于 Azure CosmosDB 支持它们，并且随着 4.0 版本的发布，MongoDB 现在支持多文档事务。

这对我们意味着什么呢？

首先，在许多情况下，即使不同实体之间的交易是可能的，也可能是不期望的。我们可能必须在设计或性能上做出妥协才能让它工作，因为执行多实体事务的技术总是会增加一些开销。即使在 SQL-land 中，我们仍然经常需要担心一致性、锁、并发性等。

我发现最常被忽视的场景是桥接不同的资源，而不是同一资源中的多个实体(例如许多 NoSQL 数据库)。我在博客上写过关于[弹性模式](https://jimmybogard.com/refactoring-towards-resilience-a-primer/)的文章，特别是将调用 API 结合到外部资源上。不过，最常见的情况是，我看到有人试图向数据库写入数据，并向队列发送消息:

[![](img/29732561a296353509ec31616e480d6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--woagiKvT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/1/2019/Picture0058.png)

代码是相当无害的——我们试图在数据库中做一些事情，这是跨多次写入的事务性的，但同时，试图写入像 RabbitMQ/Azure Service Bus 这样的队列/代理/交换。

虽然可能很容易/明显地发现我们试图将外部服务整合到我们的业务交易中的地方，如 API，但当基础设施是我们拥有的并且 T2 是事务性的时，这就不那么明显了。

最终，我们的解决方案将是相同的，即应用发件箱模式。

### 发件箱模式一般化

使用 Cosmos DB 方法，我们将发件箱放在每个单独的文档中。这是因为我们的事务边界是单个集合中的单个项目。为了提供“发布”消息的原子性，我们需要将发件箱设计为与业务数据相同的事务范围。如果事务范围是单个记录，发件箱放在该记录中。如果范围是数据库，发件箱放在数据库中。

有了 SQL 数据库，我们的事务范围大大扩大了——扩大到整个数据库！这也拓宽了我们如何建模“发件箱”的可能性。我们不必在 SQL Server 中为每个记录存储一个发件箱——我们可以为整个数据库创建一个发件箱。

这个发件箱表格里应该有什么？类似于我们在 Cosmos DB 示例中的原始发件箱:

*   消息 ID(这是哪条消息)
*   类型(这是哪种消息)
*   正文(邮件内容)

我们还需要跟踪已经发送或没有发送的内容。在 CosmosDB 示例中，我们将发送/接收的消息保存在每个记录中。

但是对于 SQL，拥有一个无限制增长的表...有问题，所以我们需要更多关于消息是否已经被发送的信息，以及发送日期，以便能够在我们之后清理。

但是，我们可以将两者合并成一个值——“DispatchedAt”，作为时间戳:

[![](img/464ba53f0d6e158fd8a9d49d107673fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XjBPQjSF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/1/2019/Picture0059.png)

处理发件箱将与我们最初的 Cosmos DB 示例稍有不同，因为我们现在只需要处理一个表。

### 通过我们的 SQL 发件箱发送

在 Cosmos DB 示例中，我们使用领域事件进行通信。我们可以在我们的 SQL 示例中做类似的事情，但是我们或多或少需要画一些界限。我们的域事件只是为了协调我们的域模型中的活动，还是作为服务级别的集成事件与外部世界进行交互？以前，我们只将它们用于域事件，并使用特殊的域事件处理程序来“重新发布”为服务事件。

每种方法之间都有很多权衡，但是一般来说，最好不要结合域事件和服务事件的思想。

为了保持灵活性，我们可以尽可能简单地对我们的消息建模，只将它们包含在`DbContext`中:

```
public Task SaveMessageAsync<T>(T message)  
{
    var outboxMessage = new OutboxMessage
    {
        Id = Guid.NewGuid(),
        Type = typeof(T).FullName,
        Body = JsonConvert.SerializeObject(message)
    };
    return Outbox.AddAsync(outboxMessage);
} 
```

当我们在做需要任何类型的消息传递的事情时，我们不是直接发送给代理，而是保存一条新消息作为事务的一部分:

```
using (var context = new AdventureWorks2016Context())  
{
    using (var transaction = context.Database.BeginTransaction())
    {
        var product = await context.Products.FindAsync(productId);

        product.Price = newPrice;
        await context.SaveMessageAsync(new ProductPriceChanged 
        {
            Id = productId, 
            Price = newPrice
        });
        await context.SaveChangesAsync();

        transaction.Commit();
    }
} 
```

每个需要发送消息的请求只需在单个事务中写入业务数据和发件箱:

[![](img/7a862c37c6c27b976a8e9e807d6dc4fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O84bO6NJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/1/2019/Picture0060.png)

然后，与我们的 Cosmos DB 示例一样，调度程序从发件箱中读取消息，并将这些消息发送给我们的代理:

[![](img/694b32f890b54392ab557979039d2ecb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zJ3BBRJo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/1/2019/Picture0061.png)

与我们的 Cosmos DB 示例一样，我们的 dispatcher 可以在事务完成后立即运行，还有一个后台进程可以在任何错过的消息后进行“清理”。每次发送后，我们会设置发送日期，以确保跳过已经发送的消息:

```
using (var context = new AdventureWorks2016Context())  
{
    using (var transaction = context.Database.BeginTransaction())
    {
        var message = await context.Outbox
             .Where(m => m.DispatchedAt == null)
             .FirstOrDefaultAsync();

        bus.Send(message);
        message.DispatchedAt = DateTime.Now;
        await context.SaveChangesAsync();

        transaction.Commit();
    }
} 
```

有了这个，我们就可以在事务中安全地“发送”消息了。但是，我们还是要处理两次收到这些消息！

### 删除重复信息

为了确保我们实现至少一次传递，但正好一次处理，我们必须跟踪我们已经处理的消息。为此，我们可以在发件箱中添加一点额外的信息——不仅仅是当我们已经发送了条消息时，而是当我们已经处理了*条消息时:*

[![](img/8650e2c560f0d0876975b8b01a2c3e16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---PObvDiM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimmybogardsblog.blob.core.windows.net/jimmybogardsblog/1/2019/Picture0062.png)

与我们的收件箱类似，我们将在每封邮件中包含处理日期。当我们处理一封邮件时，我们将仔细检查发件箱，看它是否已经被处理过。如果没有，我们可以执行这项工作。如果是这样，我们就跳过这条消息——什么也不做！

有了这些措施，最后一步是决定我们应该在发件箱中跟踪邮件多长时间。邮件可以标记为已处理的最长时间是多久，我们可能会再次收到该邮件？一个小时？一天？一周？可能不是一年，而是有意义的事。我会从大规模开始，比如说一周，然后随着我们对系统特征的理解，再慢慢降低。

使用发件箱模式，我们仍然可以通过跟踪我们需要交流的内容、交流的时间和处理的时间来协调事务资源之间的活动。这就像一个小的待办事项列表，我们的系统用它来检查事情，永远不会忘记它需要做什么。

在我们的上一篇文章中，我将总结并涵盖一些我们应该避免这种级别的协调的场景。