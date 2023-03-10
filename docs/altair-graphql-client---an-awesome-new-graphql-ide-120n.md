# Altair GraphQL 客户端——一个很棒的新 GraphQL IDE

> 原文：<https://dev.to/ankitjey/altair-graphql-client---an-awesome-new-graphql-ide-120n>

[![GraphQL](img/2891d10a864eb9aed396651834ed633a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5jpmTflT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/63gg841vdswawic7rd7q.png)
[】graph QL](https://graphql.org)已经作为经典 REST 架构的替代方案出现，并在开发人员社区中广受欢迎。在 GraphQL 开发中，您不能使用典型的客户端，如 [postman](https://getpostman.com) ，而是使用 GraphQL IDEs，通过允许他们更快地开发，使开发人员的生活更容易。但是我们的选择是有限的，因为它们中很少能够直接集成到我们的应用程序中以获得浏览器内的体验。

Altair GraphQL 客户端就是这样一个在开发人员社区中逐渐流行起来的客户端，其原因是正确的。它拥有像广受欢迎的 [Prisma GraphQL 游乐场](https://github.com/prisma/graphql-playground)等所有功能。在这篇文章中，我将介绍 Altair GraphQL 客户端的一些关键特性。

### 直接记录和添加查询

[![Query addition](img/b23ece0c11e3f16c737b9378fb4367b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PXFEsmba--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ejn6lsd2ns9l77jqzauh.gif) 
您可以在文档中查看您的 GraphQL 模式，显示方式很好，提供了有关查询、变异、类型、参数和描述的信息。您可以在文档中搜索。然而，单击即可添加查询的能力是一个独特且非常有用的特性。

### 请求的标题、变量和历史

[![Variables](img/823dc4a822c5b94355b0528dd8bc6b1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7FZIpyLb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6a78xq15stdywoo963l0.gif) 
你可以给你的请求设置头和添加变量。您还可以在历史记录中检查您以前提出的请求(也可以选择清除它们)。

### GraphQL 订阅

您可以毫无顾虑地测试您的 [GraphQL 订阅](https://www.apollographql.com/docs/graphql-subscriptions/)。只需提供订阅 URL 并开始订阅，一旦订阅，您就会收到发送的每条消息。

### 导入导出窗口状态

[![Import/Export](img/61daa664645dbc9611b5a0191d1b1b76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N2-Pssu6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ynqdr1p0qw5x3flau9am.gif) 
你可以导出你当前的窗口状态与所有的查询、变量或标题，也可以导入它或另一个。agq 文件。

### 改变语言和外貌

[![Settings](img/f606361415a9fa7c6d018572c236c4f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1Fll6jyZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k55zc6cs4fv80858nlql.gif) 
您可以更改您的语言-提供翻译。目前可用的语言包括英语，西班牙语，法语，意大利语，俄语等。你可以在明暗之间切换你的客户的主题。在“设置”中也可以使用键盘快捷键。

### 其他一些特性包括:

*   自动完成——当您在编辑器中键入查询时，会得到一个自动完成的有效术语列表。

*   美化查询——您可以在编辑器中以适当缩进的方式格式化查询，以便于阅读。

*   压缩查询——您可以将查询压缩成一个缩小的形式，删除任何不必要的空格和注释。

*   将你的 GraphQL 请求转换成 cURL 请求，你可以在其他地方使用。

*   response Stats——您可以很容易地知道您的查询需要多长时间，响应 Stats 信息针对您的每个请求进行显示。

*   语法和错误突出显示-当您键入查询时，语法和代码错误会突出显示。

*   多个窗口——您不局限于一次只打开一个窗口，而是可以同时打开多个窗口来处理多个不同的查询。

随着全球各地的贡献者添加了许多功能，Altair 正在迅速发展，成为 GraphQL 社区的首选。在我看来，Altair 解决了 GraphQL 开发人员面临的所有主要用例，我会向任何人推荐它。