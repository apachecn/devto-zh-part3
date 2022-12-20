# 上下文为王:寻找服务边界

> 原文：<https://dev.to/codeopinion/context-is-king-finding-service-boundaries-4mob>

[![Context is King: Finding Service Boundaries](img/e072c2d14d215cf90470d929e157ed54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q3XC5wMC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeopinion.com/wp-content/uploads/2019/05/crown-1.png)

拥有明确的服务边界是 SOA 的[原则之一。定义正确的服务边界至关重要。我相信开发一个系统最重要的一个方面是找到服务边界，这也很容易出错。](http://architectopia.blogspot.com/2008/01/four-tenets-of-soa.html)

这个博客系列旨在为您正在开发的系统在一个域中寻找服务边界提供指导。

*   [通过语言寻找服务边界](https://codeopinion.com/context-is-king-finding-service-boundaries-through-language/)
*   [自主服务](https://codeopinion.com/autonomous-services/)
*   [关注服务能力，而不是实体](https://codeopinion.com/focus-on-service-capabilities-not-entities/)
*   [4+1 建筑视图模型](https://codeopinion.com/41-architectural-view-model/)

## 什么是服务？

在我走得太远之前，我最好澄清一下“服务”对我来说意味着什么，这样我们才能达成共识。如果我能把它简化成一句话，那就是:

> 服务是一组业务功能的权威。

一个更长的解释是，服务拥有所有的业务逻辑和数据，这些逻辑和数据与我们领域的系统中的一组特定业务功能相关联。

以下是本系列中涉及的一些主题。

## 数据权威

服务应该对它们管理的数据拥有完全的权限。他们管理的数据应该与其提供的功能相关。任何其他服务都不能直接访问或修改数据。如果服务没有提供任何功能，它就不应该是服务，而只是一个数据库。

## 自主

服务不应该依赖于其他代码。它们应该是相互独立的。比 RPC 更喜欢异步消息传递。

服务对消息做出反应。这些消息的发布者不需要知道它们是否被处理过。消息的消费者不需要知道它们是如何创建的。

如果需要 RPC，请为您的服务创建定义良好的接口。

## 语言

对我来说，语言能对理解一个领域产生如此巨大的影响，帮助定义界限，这总是令人惊讶的。

我的经典例子是使用一个有产品实体的仓库/分销系统。产品不是产品不是产品。

这意味着一种服务中的产品可以代表另一种服务中完全不同的一组数据功能。

这让我想到了实体…

## 实体

我不确定这个行业从哪里开始关注实体。领域驱动设计可能是部分原因。它也可以像 ORM 一样，映射到代表实体的表。

不管原因是什么，我认为它们从真正重要的业务能力而不是实体上转移了注意力，从而造成了巨大的伤害。

## 博客系列

关于所有这些话题的更多内容将在其他帖子中详细讨论。如果您有任何问题或意见，请在评论区或 Twitter 上联系我。

*   [通过语言寻找服务边界](https://codeopinion.com/context-is-king-finding-service-boundaries-through-language/)
*   [自主服务](https://codeopinion.com/autonomous-services/)
*   [关注服务能力，而不是实体](https://codeopinion.com/focus-on-service-capabilities-not-entities/)
*   [4+1 建筑视图模型](https://codeopinion.com/41-architectural-view-model/)

[关注@codeopinion](https://twitter.com/codeopinion?ref_src=twsrc%5Etfw)

帖子[上下文为王:寻找服务边界](https://codeopinion.com/context-is-king-finding-service-boundaries/)最先出现在[共同意见](https://codeopinion.com)上。