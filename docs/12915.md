# 为什么您应该采用 Graphql

> 原文：<https://dev.to/devdammak/why-you-should-adoptgraphql-4d8c>

在过去的几年中，跨多个平台和设备共享数据是一个巨大的负担，为了解决这些问题，2000 年微软推出了 SOAP(简单对象访问协议)web 服务体系结构。SOAP 是一种消息协议，专门用于在互联网上跨多个设备交换结构化数据。但是 SOAP Web 架构存在一些缺陷，导致了 REST(表述性状态转移)Web 服务架构的发展。

> 表述性状态转移(REST)是一种软件架构风格，它定义了一组用于创建 web 服务的约束。符合 REST 架构风格的 Web 服务称为 RESTful web 服务，它提供了互联网上计算机系统之间的互操作性。RESTful web 服务允许请求系统通过使用一组统一的、预定义的无状态操作来访问和操作 web 资源的文本表示。

**什么是 GraphQL？**

> GraphQL 是一种 API 查询语言，也是一种用现有数据完成这些查询的运行时语言。GraphQL 为 API 中的数据提供了完整且易于理解的描述，使客户能够准确地要求他们需要的东西，使 API 更容易随时间发展，并支持强大的开发工具。

Graphql 是由脸书团队开发的语言，在 2015 年公开发布之前，它在脸书产品上内部使用，旨在提供传统 REST API 架构的替代方案。许多公司如 twitter、GitHub、PayPal、Shopify、Cousera、Stackshare e.t.c 都采用 Graphql API 作为 web 服务架构

**graph QL 优于 Rest(表述性状态转移)**

*   **不基于 HTTP 动词** : Graphql 不基于 POST、GET、DELETE、PUT、PATCH 请求。它不关心 HTTP 动词，只发送你的数据，它立即理解你想要执行的操作。

*   自动化文档:文档是一种非常有效的方式来解释你的 web 服务需要什么，所有的端点，端点参数和响应数据。Graphql 提供了一种很好的自动文档化方法。它为您提供了与您的 web 服务相关的所有必要细节，不像 REST 架构那样使用第三方包，如 swagger、Doxygen、Daux 等。

*   **单端点路由** : GraphQL 仅公开一个单端点路由，这将允许您一次性获得所有数据。REST 上的多端点路由是 REST web 服务的最大缺点之一，它对性能有很大的负面影响。假设您需要使用 ID 作为参数来获取 twitter 用户的详细信息，还需要获取用户的 tweet 以及用户的 retweet。
    使用 REST Web 服务，您可以三次访问数据库..也就是说，你有三个 API 端点..但是使用 GraphQL，您只需要在一次旅行中指定您需要的所有数据，您就拥有了您需要的所有数据

*   **数据灵活性和性能**:这允许客户端完全控制 web 服务将返回的数据，这有助于删除不相关的数据并优化性能。GraphQL 还为客户/开发人员提供了一个处理数据的平台。