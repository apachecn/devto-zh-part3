# 使用 AWS Amplify GraphQL 转换库进行 GraphQL API 认证和授权

> 原文：<https://dev.to/dabit3/graphql-api-authentication-authorization-with-the-aws-amplify-graphql-transform-library-3gn1>

在我听到的所有关于 GraphQL 的讨论和问题中，出现最多的一件事是如何正确地实现 API 的认证和授权。

[![](img/9de1df74a6a3f36d1966bce6dd3ce90b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--en4tONHg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9l3wu2194cbdl0vqgrsd.jpg)

问题是这些问题不容易解决，为了保护数据库中的数据，必须正确解决这些问题。

根据您的 GraphQL 实现，这在不同的 API 之间可能会有很大的不同。在这个视频中，我演示了如何向 AWS AppSync API 添加身份验证，然后使用 [GraphQL 转换](https://aws-amplify.github.io/docs/cli/graphql?sdk=js)库在 GraphQL 模式上实现授权规则。

[GraphQL 转换库](https://aws-amplify.github.io/docs/cli/graphql?sdk=js)提供了一个简单易用的抽象，帮助您在 AWS 上快速创建 web 和移动应用程序的后端。

使用不同的指令，如`@auth`(认证)、`@function`(添加 Lambda 函数解析器)和`@connection`(创建类型之间的关系)，您可以声明性地将不同的功能实现到您的 API 中。

在这个视频中，我们来看看如何用两种 GraphQL 类型创建一个示例应用程序:`Post`和`Note`。对于`Post`类型，我们希望将其视为类似于您在博客上看到的内容，所有者可以更新和删除帖子，但任何人都可以阅读。对于`Note`类型，我们对其进行配置，以便只有创建注释的人可以读取、更新或删除它。

在部署了 API 之后，您可以进一步配置解析器，因为您想要实现应用程序特有的业务逻辑。