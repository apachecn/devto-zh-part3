# GraphQL 性能问题&一个简单的解决方案

> 原文：<https://dev.to/graphqleditor/graphql-performance-issues--an-easy-solution-ho7>

GraphQL 最大的缺陷之一是缺少一些对应用程序性能至关重要的基本实现。一个简单的 [GraphQL 服务器](https://graphql.org/graphql-js/running-an-express-graphql-server/)没有内置的缓存或批处理机制。这可能会给你的应用程序带来一些问题。

### 多余的通话...很多多余的电话

GraphQL 是关于查询对象上的特定字段。`fields`是独立的函数，这意味着简单的 GraphQL 服务器可能会在每次解析`field`时产生冗余调用来发出新的数据库请求。

我们以一个简单的 [GraphQl 博客](https://blog.graphqleditor.com/)为例。我们的博客将使用分页来加快加载速度，假设我们想一次显示 5 个。

我们想得到我们的帖子以及它们的作者的名字。我们作者的详细信息是一个标准的`user`字段，它有自己的解析器，因此我们需要:

*   1 要求列出我们的博客帖子
*   5 个调用来获取每个帖子的作者(我们需要为每个解析器获取一个用户)

但是这些帖子有评论怎么办？假设每篇博文有 2 条评论，当然，每条评论都有作者，如下所示:

*   1 征集博客帖子列表
*   5 每个帖子的作者征集
*   5 次调用以获取每个帖子的评论列表
*   10 次电话询问评论作者

这给了我们 21 个电话。如果我们在每个帖子下有 10 或 20 条评论会怎么样？调用的数量将激增，这将导致应用程序加载时间的严重问题。

[![](img/9abee7321c90fe14a467c36df9df2b54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--913Ljczp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/2v9ltl.jpg)

### 批处理&缓存

还好有解决的办法！DataLoader 是由脸书开发团队开发的一个实用程序，可以让你批处理和缓存数据库调用。

**批处理**是 DataLoader 的主要特性。当 Dataloader 看到你多次点击同一个表时，它会将所有调用一起批处理，即 5 个博客帖子的作者& 10 个评论的作者会在一次调用中被批处理。

首先，您需要通过提供批量加载功能来创建加载器。

```
var DataLoader = require('dataloader')

var userLoader = new DataLoader(keys => myBatchGetUsers(keys)); 
```

该函数接受一组键，并返回一个解析为一组值的承诺。您需要从加载器中加载单个值。DataLoader 将合并同时发生的单个加载，然后用所有请求的键调用您的批处理函数。

```
userLoader.load(1)
  .then(user => userLoader.load(user.invitedByID))
  .then(invitedBy => console.log(`User 1 was invited by ${invitedBy}`));

// Elsewhere in your application
userLoader.load(2)
  .then(user => userLoader.load(user.lastInvitedID))
  .then(lastInvited => console.log(`User 2 last invited ${lastInvited}`)); 
```

**缓存**是 DataLoader 的另一个特性。每个`DataLoader`实例代表一个唯一的缓存，当 DataLoader 检测到两个博客帖子、评论或它们的组合具有相同的作者时，它将重用它已经在内存中的对象，而不是进行新的数据库调用。

如果您想确保您的应用程序尽可能平稳地运行，我强烈建议尝试 DataLoader。阅读更多关于数据加载器实现的内容，查看其 [GitHub repo](https://github.com/facebook/dataloader) 或观看 GraphQL 创建者之一 [Lee Byron](https://twitter.com/leeb) 的*“数据加载器-源代码演练】*:

[https://www.youtube.com/embed/OQTnXNCDywA](https://www.youtube.com/embed/OQTnXNCDywA)