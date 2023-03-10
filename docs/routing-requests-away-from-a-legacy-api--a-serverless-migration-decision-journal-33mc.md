# 从遗留 API 路由请求

> 原文：<https://dev.to/paulswail/routing-requests-away-from-a-legacy-api--a-serverless-migration-decision-journal-33mc>

上一次，我就如何进行迁移做出了第一个重大决定。在将数据库从 MongoDB 迁移出去之前，我决定将 API 代码从 Express.js 重写为 API Gateway/Lambda。

我的第一个准备任务是创建 2 个 AWS 帐户，用于在隔离的开发/试运行和生产环境中托管新资源。我已经完成并记录在这里。

## 原样架构

在我决定下一个要解决的项目之前，下面是当前架构的样子:
[![Legacy Autochart API architecture](img/605871ebecaaba873104f1ebde487d2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4afKBU-W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://winterwindsoftware.com/img/blog-images/AutochartLegacyAPIArchitecture.png)

许多服务器资源(ELB、EC2 实例、ECS 集群、Docker 容器、VPC、MongoDB、Elasticache)我最终想要完全取代或废弃。

`PortalApp container`资源是运行 Node.js 进程的 Docker 容器，该进程托管整个 Express.js 遗留 API。来自它的绝大多数下游调用是对 MongoDB 数据库的调用，尽管有一些 API 路由调用其他服务，如 Redis。
API 上游有:

1.  AngularJS 单页应用程序，是最终用户的主要用户界面
2.  使用 API 进行同步数据检索调用的第三方集成
3.  上游“事件跟踪”服务。该服务使用 API 网关和 Kinesis 从客户网站捕获点击流。Lambda 处理程序监听 Kinesis 事件，然后调用遗留 API，根据 MongoDB 中的访问者记录保存捕获的事件数据。

SPA (1)和第三方集成(2)使用的 REST API 端点的契约不能更改，尽管在更改内部 API 调用的接口方面有更大的灵活性(3)。

## 开始增量迁移

我的一个限制是我需要小块地迁移。大爆炸切换不是一个选项。为此，我需要确定这些小块是什么，以及如何将每个小块部署到生产中。换句话说，我现在需要回答两个问题:

1.  我应该从哪些 API 路径开始迁移？要回答这个问题，我需要确定 monolith API 中的微服务边界。
2.  **我如何有选择地拦截对新的无服务器实现的入站 API 请求？**要回答这个问题，我需要确定一个[【扼杀者】](https://dev.to/kylegalbraith/how-to-breakthrough-the-old-monolith-using-the-strangler-pattern-63e)机制来使用。

我不确定先攻击哪一个更有意义。一个依赖另一个吗？让我们再深入一点…

### 选项 1:从识别微服务边界开始

*   ✅一旦完成，我会对整体范围和所涉及的工作有一个更好的了解，并能制定一个有序的计划。
*   做这个分析的✅应该会发现代码重写中最危险/最复杂的部分，特别是那些我至今还没有考虑到或者潜意识里低估了的部分。
*   ❌这将需要一段时间来完成，因为我需要分析整个代码库，以确定每个 API 调用的所有上游和下游依赖关系。这些代码中的一些是 6 年前写的，所以在我的脑海中并不新鲜。

### 选项 2:从请求拦截机制开始

*   ✅:我已经知道，所有对传统 API 的入站请求都是通过 HTTPS 公开发送到同一个 URL 子域(使用自定义身份验证和授权)，甚至是由作业/微服务发出的“内部”API 调用。所以我应该只需要把拦截钩放在一个地方。
*   ✅该机制将位于新代码的上游，因此了解新代码将如何被调用，以及它将能够以何种粒度级别拦截请求(例如，通过 URL 路径、动词、其他请求参数/头)似乎是合乎逻辑的。
*   ✅:我可能希望使用 IAM 作为内部 API 调用的认证机制，所以我需要看看拦截机制是否能支持这一点。
*   ✅这应该最多在几个小时内就能实现。
*   ❌:为了在生产环境中测试这一点，我需要确定至少 1 条(低风险)迁移和部署路径(或者只是在 API 中添加一个新的虚拟端点)

我将从选项 2 开始，因为我应该能够很快完成这项工作。

## 在哪里做拦截？

这里我可以采用两种通用方法:

1.  使用现有架构中具有路由功能的组件，并将其配置为将“迁移”的路由转发到新代码，并保持其余部分不变。
2.  在执行路由的现有架构前添加新资源。

对于方法#1，这里的好处是所有现有的请求(还没有被迁移)都将通过他们一直做的同一个前门进来。这里显而易见的选择是使用弹性负载平衡器。

然而，当前的架构使用经典的 ELB，[基于路径的路由仅使用应用负载平衡器](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/tutorial-load-balancer-routing.html)来支持。将它切换到应用程序 ELB 似乎有风险，因为所有新的请求现在都将通过一个新的组件(否定了这种方法的主要优点)。

另一个选项是更新 Express 应用程序，以便转发新请求。但是这看起来非常混乱，并且意味着所有请求仍然受到 EC2 实例能力的约束。我不希望每次有新路由准备迁移时都必须部署新版本的 Express 应用程序。

剩下第二种方法。这里显而易见的选择是 API Gateway，因为它允许基于路径的路由，这也是我将在新的无服务器 API 中使用的。缺点是对现有架构的改动比我希望的要大一点(因为所有请求现在都要通过一个新的前门)。但我认为这是一个可以接受的风险，我可以通过一些简单的测试来减轻风险，并且如果出现问题，只需将 DNS 指向 ELB 就可以轻松回滚。

下面是请求路由的流程:

[![Autochart Migration Request Routing](img/52b7038a8775e5d6326bd44da7d03627.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uUU4b7-4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://winterwindsoftware.com/img/blog-images/AutochartMigrationRequestRouting.png)

## 下一步

既然我已经决定使用 API Gateway 作为我的前门路由器，我的下一步将是部署一个具有单一测试路由和 Lambda 函数处理程序的网关实例，然后更新 DNS 条目以指向它。我将使用[无服务器框架](https://serverless.com/framework/)来管理路由配置并使部署更容易。

我将在下一篇文章中向您更新这方面的内容，届时我应该已经将第一段移植代码投入生产了。🤞

✉️ *如果你想在这一系列准备就绪后尽快获得未来的更新，并且**访问我用来跟踪我在这个项目中执行的所有任务的 Trello 板**，你可以[在这里订阅](https://winterwindsoftware.com/serverless-migration-journal/#signup)。*

* * *

您还可能会喜欢:

*   [担心无服务器带走](https://winterwindsoftware.com/concerns-that-serverless-takes-away/)
*   [“无服务器”的不同定义](https://winterwindsoftware.com/serverless-definitions/)
*   [无服务器词汇表](https://winterwindsoftware.com/serverless-glossary/)

*原载于 winterwindsoftware.com*。