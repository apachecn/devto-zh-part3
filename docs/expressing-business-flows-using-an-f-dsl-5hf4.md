# 使用 F# DSL 表达业务流

> 原文：<https://dev.to/jamesnovino/expressing-business-flows-using-an-f-dsl-5hf4>

在两年多的时间里，订单管理团队构建并迁移到了一个新的工作流系统，我在之前的帖子中解释了其中的一些原因:
工作流的微服务:Jet.com 订单管理系统的发展

TLDR:将业务领域表达为工作流简化了开发并提高了整体的可维护性。更多详情请看之前的帖子:)。这篇文章详细介绍了我们如何使用 F#将我们的工作流建模为 DSL。

更多阅读:
[https://medium . com/jet tech/micro services-to-workflows-expressing-business-flow-using-an-f-DSL-d 2e 74 e 6d 5d 5 e](https://medium.com/jettech/microservices-to-workflows-expressing-business-flows-using-an-f-dsl-d2e74e6d6d5e)