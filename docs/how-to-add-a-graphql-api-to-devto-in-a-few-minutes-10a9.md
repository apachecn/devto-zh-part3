# 如何在几分钟内将 GraphQL API 添加到 dev.to 中

> 原文：<https://dev.to/rhymes/how-to-add-a-graphql-api-to-devto-in-a-few-minutes-10a9>

## 你为什么要读这个

*   dev.to 有一个 REST API，可能会在不久的将来公开
*   GraphQL 现在是一项可靠且经过验证的技术，也是许多人在 2019 年想要熟悉的技术之一
*   PostgreSQL(dev . to 使用的)是我最喜欢的关系数据库

我决定尝试 [Hasura](https://hasura.io/) ，它承诺让人们构建一个直接与 PostgreSQL 数据库对话的 GraphQL API。

我没想到 15 分钟后就完成了演示，只编写了 GraphQL 查询本身和一条 SQL 语句。完全没有其他代码。

让我回顾一下，解释一下哈苏拉是什么。

## 哈苏拉

该工具是用 Haskell 和 JavaScript 编写的 GraphQL 引擎/服务器。它位于客户端应用程序(例如 API 消费者)和数据库(PostgreSQL)之间。

客户端发送 GraphQL 查询，服务器获取它，解析它，验证它，将其转换为高度优化的 SQL 查询，获取 JSON 格式的数据(PostgreSQL 允许您在 JSON 中导出数据，而无需返回到 web 应用程序，如果您问我，这是它最近最好的功能之一)并将其发送回客户端。

如果你想了解更多关于架构的内容，你可以阅读 Hasura 的文章:[高性能 GraphQL 到 SQL 引擎的架构](https://blog.hasura.io/architecture-of-a-high-performance-graphql-to-sql-server-58d9944b8a87)。

因此，出于好奇，我只读了那篇文章，决定尝试阅读尽可能少的文档。

很顺利。

## 设置服务器

设置非常简单。我安装了 Mac 版的 Docker，[下载了一个脚本](https://docs.hasura.io/1.0/graphql/manual/deployment/docker/index.html)，运行它的内容并等待:

```
docker run -p 8080:8080 -e HASURA_GRAPHQL_DATABASE_URL=postgres://username:password@host:port/db -e HASURA_GRAPHQL_ENABLE_CONSOLE=true hasura/graphql-engine:v1.0.0-alpha34 
```

Enter fullscreen mode Exit fullscreen mode

之后，我用浏览器打开控制台，出现了以下内容:

[![](img/49107aba29019c70b71a588ae3109baf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7aqeL8N3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/166hfheajci0syvof93c.png)

## 准备模式

点击`Data`显示所选数据库中包含的表格列表。我指示 Hasura 暴露`articles`、`comments`和`users`表，在 UI 中点击一下,[在它们之间创建关系](https://docs.hasura.io/1.0/graphql/manual/schema/relationships/index.html),结果如下:

[![](img/8ed2e08cb30183d239e47fe89a7d4f68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8_dI-0TK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xj0x1aw7z36lllg6pj1j.png)

我唯一需要手动设置的是默认 dev.to 数据库中不存在的`articles_comments`。

Hasura [不支持](https://github.com/hasura/graphql-engine/issues/1167) [多态关联](https://guides.rubyonrails.org/association_basics.html#polymorphic-associations)，因为它们是由 Ruby on Rails 创建的。搜索[问题跟踪器](https://github.com/hasura/graphql-engine/issues/1167)找到的解决方案是创建一个简单的视图:

```
create view articles_comments as select * from comments where commentable_type  = 'Article'; 
```

Enter fullscreen mode Exit fullscreen mode

### 查询数据

有了模式和关系设置，我接着写了几个带有和不带有嵌套数据的 GraphQL 查询，看看是否一切正常。

**最后 10 位用户及其最后 3 条评论**

[![](img/698c0543461b8d52fa14242a811b6ec7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MXd-f8xP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0nxegkrnydb451qvh45j.png)

**最受欢迎的文章、最受欢迎的评论及其作者的姓名**

[![](img/0ad790c0e4c75b01fd6858df9448abbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MmLDpEMS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tm275qvs9eirarq0sq3m.png)

这最后一个查询是 GraphQL 优于 REST 的原因之一。当您需要嵌套或相关的数据时，REST(ish)API 往往是一种累赘。想想看，我们要求 web 服务器检索反应最多的帖子，然后获取赞数最多的评论及其作者。

在经典的 REST(ish) API 中，您会调用 HTTP web 服务器三次:

*   给我按受欢迎程度排序的文章
*   给我按受欢迎程度排序的评论，这里我给你文章的 id
*   给我用户，这里我给你评论 id

在一些 REST APIs 中，您可能会得到一个 HTTP 调用，但这是可能的，而不是默认的。在这种情况下，GraphQL 降低了障碍:

*   任何 GraphQL 客户端都可以与您的服务器对话，而无需使用您自己版本的 REST
*   往返只有一次
*   API 是自文档化的，这在探索阶段是一个巨大的优势

单击 Hasura 界面中的“Analyze”按钮会产生 SQL 和执行计划:

[![](img/f4a2e30888c25b02b0d42535e46a1014.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u97w-n67--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jeranf4zxm5ol0qt28dv.png)

我就讲到这里，写了这篇文章。尽管 Hasura 支持[许多其他特性](https://hasura.io/all-features):

*   突变
*   订阅(基本上是数据库中数据变化的实时通知，您可以将其连接到 UI)
*   授权和行级访问控制(您可以决定公开什么，不公开什么)
*   模式拼接(外部 GraphQL APIs，需要通过您的透明公开)
*   事件上的网页挂钩
*   迁移

Hasura 还部署在 Heroku、Digital Ocean 和 Kubernetes 上。

我只尝试了几分钟，所以我不能说什么，但对于那些想在现有数据库基础上构建 GraphQL API 的人来说，这无疑是一个有趣的工具。