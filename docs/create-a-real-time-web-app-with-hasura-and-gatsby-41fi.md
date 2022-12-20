# 用 Hasura 和 Gatsby 创建一个实时 web 应用程序

> 原文：<https://dev.to/hasurahq/create-a-real-time-web-app-with-hasura-and-gatsby-41fi>

一个多月前，我们直播了如何用 Hasura 创建 Gatsby 博客。这篇博文总结了一下。如果你没有看过这个视频以及我们在 Youtube 频道上的其他视频，我强烈建议你去看看

[https://www.youtube.com/embed/HTEGGndT3zY](https://www.youtube.com/embed/HTEGGndT3zY)

# 我们在创造什么。

想法是创建一个由 GraphQL 后端支持的简单博客。对于后端，我们选择了 [Hasura](//hasura.io) ,因为我在这篇博文中列出了很多原因:

> ![Vladimir Novick profile image](img/cab63c0f6b10e8fb20593e2a65c5449c.png)弗拉迪米尔·诺维克[@弗拉迪米尔诺维克](https://dev.to/vladimirnovick)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我刚刚发布了毫不费力的实时 GraphQL API 与无服务器业务逻辑运行在任何云[link.medium.com/DET666lI3T](https://t.co/qr5UNZTGYJ)上午 11:47-05 2019 年 2 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1092751461824585728)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1092751461824585728)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1092751461824585728)

我们首先将 Hasura 部署到 Heroku 并创建一堆表，用外键连接它们，然后 Hasura 立即为我们生成了查询订阅和变异。尽管我们有一些奇怪的行为。列在下降，字段在变化。一开始有点奇怪，然后我们意识到有人在跟着我们。因为端点不安全且启用了控制台，所以人们能够连接到它并更改架构。

当我们意识到我们通过使用`HASURA_GRAPHQL_ADMIN_SECRET`环境变量保护了一个端点时，我们试图对我们的观众保密。最后，一切都稳定了，人们再也不能骚扰我们了。

# 创建盖茨比网站

我们通过运行
`gatsby new . gatsbyjs/gatsby-starter-hello-world`创建了我们的 gatsby 站点

获得最基本的设置。

# 将 Hasura GraphQL 集成到 Gastby 中

我们安装了 [gatsby-source-graphql](https://gatsbyjs.org/packages/gatsby-source-graphql) ，并将其配置为与`HASURA_GRAPHQL_ADMIN_SECRET`一起工作，但在现实世界场景中，我们将拥有授权承载令牌，因此我们的 gatsby.config 将如下所示:

```
const fetch = require("isomorphic-fetch")
const createHttpLink = require("apollo-link-http")

/// Rest of the code

{
  resolve: "gatsby-source-graphql",
  options: {
    typeName: "hasura",
    fieldName: "blog",
    // Create Apollo Link manually. Can return a Promise.
    createLink: () => {
     return createHttpLink({
       uri: 'hasura graphql endpoint',
         headers: {
           'Authorization': `bearer ${token}`,
         },
         fetch,
    })
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于认证的信息，你可以在我的博客文章[中阅读](https://dev.to/hasurahq/hasura-authentication-explained-2c95)

在我们的直播流中，为了简单起见，我们使用了我们的管理秘密。

> 注意:您不能在生产环境中使用管理机密，因为您的客户端将能够对数据库进行任何更改

因此，此时我们能够查询我们的`blog`字段

# 创建页面

我们开始通过导出查询并引用 blog 字段中的数据来创建页面。基本上，在`blog`字段下，我们有所有可以用 Hasura 引擎运行的查询。