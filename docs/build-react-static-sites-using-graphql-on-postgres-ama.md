# 在 Postgres 上使用 GraphQL 构建静态网站

> 原文：<https://dev.to/hasurahq/build-react-static-sites-using-graphql-on-postgres-ama>

**TL；DR:** 为您的 [react-static](https://github.com/nozzle/react-static) 站点提供来自您现有 postgres 数据库的源数据。即时设置。教程/样板文件👉 [react-static-graphql](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/react-static-graphql)

<figure>[![](img/bb0ab1d04a9a0f090d14ea79143e1388.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dsVAFpt7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/02/reactstatic.png)

<figcaption>react-静态<-graph QL<-Hasura<-Postgres</figcaption>

</figure>

react-static 是一个用于 react 的渐进式静态站点生成器。它被设计成高性能的，并支持像 GraphQL 这样的定制查询层。这是一个流行的 JAMStack 框架。

在这篇文章中，我们将关注 **JAMStack** 的 **API** 部分，它允许我们构建强大的静态站点。所有的数据库操作都被抽象成 GraphQL APIs(在本例中是 Hasura ),并通过 JavaScript 在 HTTPS 上访问。Hasura 在 Postgres 之上提供了 GraphQL APIs。因此可以有效地利用它来构建一个带有 **react-static** 的站点，这将是 JAMStack 的**标记**部分。

<figure>[![](img/00f1d5dc2a6e6a44a41e9fc2158fa2b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OFGWltS3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/02/jamstack-4.png) 

<figcaption>JAMStack 与 Hasura GraphQL 和 react-static</figcaption>

</figure>

### 部署 Hasura

按照[自述文件](https://github.com/hasura/graphql-engine/blob/master/community/sample-apps/react-static-graphql/README.md)中的说明部署 Hasura，并创建 app 所需的表格**作者**和**文章**。注意 GraphQL 端点的 Heroku URL。您将在应用程序中对此进行配置。

### 克隆并运行 app

[演示应用](https://github.com/hasura/graphql-engine/tree/master/community/sample-apps/react-static-graphql)带你构建一个简单的博客应用，列出所有作者和每个作者写的文章。您可以克隆并运行该应用程序进行演示。

```
$ git clone git@github.com:hasura/graphql-engine.git
$ cd graphql-engine/community/sample-apps/react-static-graphql
$ yarn install 
```

安装之后，您必须配置上面得到的 GraphQL 端点。

打开`src/apollo.js`并配置端点。

现在运行 app:

```
$ yarn start 
```

您应该会看到 react-static 主页如下所示:

<figure>[![](img/d7b4a3e191b92b16a5d50d17a5750996.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qkoCRFDX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/02/Screenshot-2019-02-11-at-7.46.39-PM.png)

<figcaption>react-静态演示 app with Hasura GraphQL</figcaption>

</figure>

如果您已经向 readme 中提到的作者和文章添加了示例数据，您可以导航到 **/blog** 并在 UI 上查看数据模板。

## 实现细节

既然您已经查看了演示应用程序，那么让我们来看看幕后发生了什么。对于这个应用程序，我们需要从 Postgres 获取数据，并在 react 组件上将其模板化。要提取的数据很简单；文章和作者。

Hasura 中获取所有作者的 GraphQL 查询类似于

```
query {
  author {
    id
    name
  }
} 
```

获取一个作者写的所有文章的查询看起来像这样

```
query($author: Int!) {
    article(where: {author_id: {_eq: $author}}) {
        id
        title
        content
        created_at
    }
} 
```

就是这样！这些是构建这个演示应用程序所需的唯一查询。那么路线呢？嗯，实际上写它们很容易。react-static 读取一个名为 **static.config.js** 的配置文件，该文件可以导出一系列配置选项， [**getRoutes**](https://github.com/nozzle/react-static/blob/master/docs/config.md#getroutes) 就是其中之一。它应该解析一个路由对象数组。

我们的目标是创建一个列出所有作者的路径和一个列出给定作者写的所有文章的路径。因此，我们将有一个显示作者列表的 **/blog** 路径和一个显示特定作者撰写的所有文章列表的 **/blog/:authorId** 路径。

现在使用上面的 GraphQL 查询，我们可以编写覆盖上面用例的路由。static.config.js 的代码片段如下所示: