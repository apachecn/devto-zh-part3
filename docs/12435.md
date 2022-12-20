# 深入 OpenAPI

> 原文：<https://dev.to/steelwolf180/diving-into-openapi-3k2g>

[![Photo by Shifaaz shamoon on Unsplash](img/01e435c80b14470e518eb8a897873628.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oAULpVFj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mjn5g0htevw804ovsu81.jpg)

# 简介

本周初，我开始认真学习使用 OpenAPI 编辑器，这是我工作中一个项目的 API 开发过程的一部分。

尽管是一个**倡导者**使用它来帮助我们的 **API 开发**过程，它对第三方开发者的**文档**有用，只要他们使用我们的技术，就可以集成我们的 API。

# 文档是 API 开发的关键

[![thought-catalog-217861-unsplash](img/f6f99830e38374576ba8e4f6b339d4bb.png)](//images.ctfassets.net/ly2f59p4unnn/3AKdWL9AQVT88mYySLTHtF/6d379755faa7172aa43b22721c3057e9/thought-catalog-217861-unsplash.jpg)

在阅读了多篇关于 REST API 开发的文章后，**文档**是**帮助**推动** API 在开发者或对采用 API 感兴趣的人中采用的关键**原因之一。

这是使用 OpenAPI 规范作为我们开发过程的一部分来构建 REST APIs 的进步。

因为我在构建 API 方面的知识主要基于像 **Postman** 这样的工具和像 **Django REST** 框架这样的库。

在那段时间里，直到我与公司的其他开发人员交流时，我才发现对 OpenAPI 的需求。

这归结为**将你的 API 文档作为**单一事实来源**共享**。

这样**每个人**都可以从后端、前端、自由职业者或项目经理的角度**建设&集成**这些 API 的**向**努力。

# 什么是 OpenAPI？

[![emily-morter-188019-unsplash](img/a30f0f44b78c262590514bd17bc2a477.png)](//images.ctfassets.net/ly2f59p4unnn/7fU8I8rAITEeWaptEsqHR5/096ccc878b43a2f8a3ffd0e5a2f74cc8/emily-morter-188019-unsplash.jpg) 
OpenAPI 从 Swagger 或者 OpenAPI 规范中有多个名称。当我浏览文章或视频时，我被它弄糊涂了。

简而言之，OpenAPI 是**基于文本的**文档，它产生你的 API 的**数据可视化**，而不用给它你的源代码。这使得 API 的集成和设计变得容易。

基于 **OpenAPI 规范**，该规范在捐赠给 **Linux 基金会**之前曾被称为 **Swagger 规范**。

该规范由一家名为 **SmartBear** 的公司捐赠，该公司围绕 **Swagger 规范**创建工具和生态系统。

到目前为止，无论何时你需要 OpenAPI 工具的详细列表，你都可以去 **Swagger** 了解他们的工具，为你的 REST APIs 构建你自己的 OpenAPI 文档。

# 使用的第一反应

[![frank-mckenna-252908-unsplash](img/98cd0373a93e8547424415d105ac7be3.png)](//images.ctfassets.net/ly2f59p4unnn/3BlT56nBFBo78gBrbkiDvr/73be45c063d55758823b29c9bee6b830/frank-mckenna-252908-unsplash.jpg) 
通过使用 **Swagger 编辑器**，创建 API 文档的障碍**要容易得多**。

因为它有自己的旧格式的示例 API 文档，即 **swagger 2.0** 。这允许您开始编辑它以满足您的需要。

当我在阅读示例 OpenAPI 文档时，我偶然发现了一个很好的特性，那就是**模型**部分。

本节提供了**数据结构**,它可以用来基于创建的 API 创建您自己的数据库。

如果你正在创建你的第一个 **OpenAPI 文档**，我会坚持使用 **OpenAPI v3** 。

因为在版本 3 中，OpenAPI 规范已经被大大简化了，为每个 API 增加了额外的 T2 安全部分，比如使用 OAuth 或 JWT。

幸运的是，开始使用 OpenAPI v3 非常容易，因为 T2 斯瓦格 T3 已经在他们的文档部分 T4 基础结构 T5 为你提供了基础结构。

# 结论

[![marc-olivier-jodoin-502572-unsplash](img/11b02b520c41cfa615449ecfb830ab74.png)](//images.ctfassets.net/ly2f59p4unnn/vECVhBWAAVrlDgfAtsB8C/3ac67e4c2cf5aa10abef2e6409a43529/marc-olivier-jodoin-502572-unsplash.jpg)

到目前为止，在花了几个小时阅读了由 **Swagger** 和我的同事编写的示例文档之后，我能够理解实现 OpenAPI 规范版本 3。

这使我能够将我的一个同事的 OpenAPI 规范文件升级到版本 3。

如果你的构建**使用 REST API**或者拥有**现有的 API**，我建议你为你的 API 构建**文档**，这样考虑你的 API 的**开发者**或者**顾问**更容易采用 **API。**

你可以在 REST API 的**设计过程**中从 **OpenAPI** 开始，或者通过使用 **APImatic** 将你的 API 转换成 OpenAPI。

最后，OpenAPI 是行业标准之一，被那些使用 API 的公司广泛采用，如 **Google** 、 **IBM** 和 **Google** 以及大量工具，如 **Postman** 。

# 参考文献

*   [如果搜索 Swagger 或 OpenAPI 没有结果，我会尝试邮差收藏](https://apievangelist.com/2018/07/16/if-search-for-swagger-or-openapi-does-not-work-i-try-postman-collection/)
*   [OpenAPI 让我觉得我已经掌握了 API 的功能](https://apievangelist.com/2018/04/18/openapi-makes-me-feel-like-i-have-a-handle-on-what-an-api-does/)
*   [开放 API 倡议](https://www.openapis.org/)
*   [大摇大摆](https://swagger.io/)
*   [利用 OpenAPI 进行更好的设计](https://www.youtube.com/watch?v=uBs6dfUgxcI)
*   [从易趣的角度看 open API](https://www.ebayinc.com/stories/blogs/tech/openapi-an-ebay-perspective/)
*   [OpenAPI:不仅仅是文档](https://konghq.com/blog/leveraging-openapi-awesome-apis/)

如果你喜欢我的文章，要么注册 max[adventurer ' s newsletter](http://eepurl.com/dOUoUb)或者你可以**关注**来获取我在 **Dev** 上的文章的**最新**更新

这篇文章最初发表在 max 的博客上，时间是[潜入 OpenAPI -阅读 3 分钟](https://www.maxongzb.com/diving-into-openapi-read-3-mins/)和[照片，由 Shifaaz shamoon 在 Unsplash 上拍摄](https://unsplash.com/photos/AX4IP3j8cB0)