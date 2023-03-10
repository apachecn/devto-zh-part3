# 用无服务器函数和 Postgres 构建有状态应用

> 原文：<https://dev.to/hasurahq/building-stateful-apps-with-serverless-functions-and-postgres-2e4h>

<figure>[![](img/324f69ed74ff265281202dd77de33b7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xNyFCmDA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/03/Screenshot-2019-03-03-at-11.55.03-PM.png) 

<figcaption>你的无服务器函数应该如何与数据库接口来管理应用程序状态？</figcaption>

</figure>

这篇文章探讨了在无服务器函数中编写业务逻辑时处理应用程序状态的不同方法。在讨论这些不同的方法时，我们将主要使用 Postgres 作为数据库，并且经常使用 [Hasura](https://hasura.io) 作为无服务器函数和 Postgres 数据库之间的粘合解决方案之一。然而，这篇文章中的观点是通用的，可以应用到你最喜欢的数据库解决方案中。

***TL；*博士**

*   [方法#1](#1) :从你的无服务器功能直接连接到 Postgres
*   [方法#2](#2) :从您的无服务器功能中使用 BaaS APIs
*   [方法#3](#3) :用丰富的事件负载触发 Postgres 事件上的无服务器功能
*   [方法#4](#4) :结合上面的方法，使用 Postgres 来编排一个工作流，其中无服务器功能驱动状态转换

## 简介

无服务器平台不允许开发人员编写保持任何长期状态的代码。这是因为无服务器运行时是按需启动的，并且是短暂的，即在请求完成后所有的资源都被释放。

以下是几个例子，说明当你编写一个普通的 API 服务器时，你不能以同样的方式来处理这些事情，并且这些事情可能是理所当然的:

1.  直接写入磁盘，就像将文件保存到文件夹中一样
2.  一个保存跨多个请求的信息的全局变量，比如一个`counter`，它跟踪这个 API 服务器已经看到了多少个请求。
3.  一个数据库连接池，即使在没有请求的情况下，也由 API 服务器维护

在本帖中，我们探索了一些真实世界用例的模式，它们可以帮助你构建下一个具有无服务器功能的应用。让我们逐一介绍！

## 1。直接连接到 Postgres】

为服务器端应用程序存储应用程序状态的最常见方式是使用数据库。然而，天真地从无服务器函数*直接连接到数据库*并不是最明智的想法，因为虽然您可以突然调用 1000 个无服务器函数，但数据库不能突然扩展到创建 1000 个数据库连接。

[![](img/42d1a285f8893263e4059ed3b24f5290.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GlUW4nKV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/03/Screenshot-2019-03-04-at-12.00.19-AM.png)

以下是连接到 Postgres 以从无服务器功能中读取/写入数据或运行数据库事务的一些最佳实践:

1.  通过遵循云供应商的最佳实践来创建到数据库的连接，这有助于无服务器功能跨调用重用数据库连接(有点像连接池):
    *   [AWSλ](https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html)
    *   [谷歌云功能](https://cloud.google.com/functions/docs/sql#best_practices_for_cloud_sql_in_cloud_functions)
    *   [Azure 功能](https://docs.microsoft.com/en-us/azure/azure-functions/manage-connections)
2.  在函数开始时创建完整的状态。
    *   这是为了避免对数据库执行多次调用，这会导致性能成本和运行成本。格温·沙皮拉在这篇博文中谈到了更多关于这种数据库访问模式的内容:[https://thenewstack.io/managing-state-in-serverless/](https://thenewstack.io/managing-state-in-serverless/)
3.  尽可能使用无 ORM 的数据层。
    *   这是因为使用 ORM，您需要首先定义数据模型，并且您必须为每个功能都这样做。这可能意味着编写大量样板文件，并使它们与底层数据库保持同步。当你这样做的时候，你会意识到你最好编写一个整体的 API 服务器，而不是彼此独立的无服务器函数！
    *   考虑使用好的 SQL 客户端或者不使用 ORM 客户端，比如[massie js](https://massivejs.org/)。
4.  考虑为您的数据库使用外部连接池代理，如 pgBouncer
    *   如果您使用 DB 事务，那么请记住数据库的并发限制。交易成本高，不容易扩展。根据您的负载，您可能需要利用一个可靠的外部连接池，比如 [pgBouncer](https://pgbouncer.github.io/) [ [0](https://medium.com/open-graphql/scaling-rdbms-for-graphql-backends-on-serverless-980ef24af171) 。事实上，DigitalOcean 为其托管的 Postgres 数据库提供了内置的连接池[ [1](https://www.digitalocean.com/docs/databases/how-to/postgresql/manage-connection-pools/) ]。

## 2。使用 BaaS API

管理状态最简单的方法之一是通过*而不是*自己管理它😉。

后端即服务(BaaS)解决方案在处理持久状态时抽象出一些复杂的性能和并发性问题。无服务器函数中的代码使用[HTTP API(无状态)](https://stackoverflow.com/questions/13200152/why-is-it-said-that-http-is-a-stateless-protocol)并将 BaaS 解决方案视为黑盒。BaaS 本身实际处理管理和持久化状态，即使成千上万的并发代码可能正在读取、写入或修改数据。

以下是您的无服务器功能可能使用的 BaaS 服务示例:

*   读取/写入对象或文件类型数据的 S3 风格 API。所有主要的云供应商现在都提供 S3 API。
*   从 Postgres 读取/写入数据的 Hasura GraphQL APIs
*   读取/写入搜索索引和数据库的算法

[![](img/40fa54d52bd2b919abe1fdd34c3f92b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oZqChk5v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/03/Screenshot-2019-03-04-at-12.01.56-AM.png)

使用 BaaS 的一个好处是您的函数*看起来*完全是无状态的。考虑下面的[代码](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/serverless-etl)，它使用 Algolia 来填充搜索索引:

```
const algoliasearch = require('algoliasearch');

exports.function = async (req, res) => {
  const data = req.body.book;

  var client = algoliasearch(process.env.ALGOLIA_ID, process.env.ALGOLIA_KEY); //from env vars
  var index = client.initIndex('book_index');

  index.addObjects([data], function(err, content) {
    if (err) {
      console.error(err);
      res.json({error: true, data: err});
      return;
    } else {
        res.json({error: false, data: content});
    }   
}; 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，这个函数本质上是对 BaaS 进行一次 API 调用，BaaS 使用 state 完成繁重的工作。

## 3。用“丰富的”事件负载触发 Postgres 事件的无服务器功能

事件是云供应商用来为无服务器功能提供资源，然后通过将参数从事件有效负载传递给该功能来运行该功能的底层机制。

如果事件有效负载包含运行无服务器功能所需的所有数据和上下文，那么您不需要从外部源读取数据。

这就把我们带到了编写需要状态的无服务器函数的第三种方法。您可以从数据库中捕获事件，如 Postgres，并使用它们来触发您的无服务器功能。

[![](img/3ab333596feaa5d05e81f544fc40a47c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QGyCtZjE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/03/Screenshot-2019-03-04-at-12.03.37-AM.png)

然而，为了在现实环境中有用，这些事件需要包含所有相关数据和相关环境。仅有`id`或`operation (insert/update/delete)`细节通常是不够的。

<figure>[![](img/c1a9c4ceabc20040e36a07237aafda85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sHWs2pMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/03/Screenshot-2019-03-04-at-12.12.19-AM.png) 

<figcaption>详细的事件有效载荷更有用</figcaption>

</figure>

在我们使用 Postgres 的例子中，事件有效负载通常应该包含:

1.  行数据
2.  与行相关的数据(通过其他表连接)
3.  应用程序用户会话信息(如果适用)

[Hasura 在 Postgres 上提供了一个现成的事件系统](https://hasura.io/event-triggers),如上所述，它可以通过更多上下文和详细的有效负载轻松触发事件！

## 4。Postgres &无服务器在工作流中充当状态机

大多数复杂的应用程序都有工作流或通过后台状态机转换的进程。考虑一个具有以下数据模型的食品订购应用程序:

<figure>[![](img/f22d116927feb61280ca9e7b8fbaabf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FBw6eY33--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/03/order-table-1.png) 

<figcaption>订单表</figcaption>

</figure>

整个订购工作流由重要的业务逻辑步骤组成，如验证、支付、批准、配送代理分配，可以认为是一系列到`order`表状态的转换。

<figure>[![](img/7d0eb53a37e28e08d71dd5e22a40b915.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lv2xhP5L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/03/orchestration.png) 

<figcaption>使用 Postgres 和无服务器功能编排工作流</figcaption>

</figure>

订单模型中的每一列都可以被认为是由一个特定的无服务器函数更新的，该函数运行复杂的业务逻辑，实际上适当地进行转换，并用更多的信息修改状态。

这种方法于是变成了方法 1/2 和方法 3 的混合:

*   对数据库事件触发无服务器功能(对特定列的更新触发特定的无服务器功能)
*   无服务器功能可以在通过方法#1 和#2 运行其业务逻辑的同时更新数据库中的状态！

这个想法也和 [3factor](https://3factor.app) 架构模式密切相关。

## 结论

对于大多数用例来说，构建具有无服务器功能的有状态应用程序并不难。基本思想是将繁重的工作委托给专家系统。使用这里描述的一些模式，开发人员可以在编写无服务器功能的业务逻辑时获取最大的价值。