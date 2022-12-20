# 仙丹伞应用的问题是

> 原文：<https://dev.to/jackmarchant/the-problem-with-elixir-umbrella-apps-850>

[伞形应用](https://elixir-lang.org/getting-started/mix-otp/dependencies-and-umbrella-projects.html)是包含多个混合项目的大项目。使用雨伞应用程序感觉更像被一把真正的雨伞戳到了眼睛。

关于伞形应用的目的，如何有效地管理一个不断增长的项目以及在生产中的某个地方部署应用，有一些误解。如果你正在考虑使用雨伞应用程序，我想举一个不使用雨伞应用程序的例子。

### 典型的雨伞 app

伞式应用程序旨在帮助开发人员将不同的关注点分成不同的应用程序，认为可以将它们与面向服务的体系结构(即微服务)进行比较。一个伞状应用程序可以被认为是一个单一的应用程序，并且在大多数情况下是这样部署的。

**让我们通过一个示例应用程序来思考:**
在一个应用程序中，有一个 web 组件提供流量、graphql API 的类型和解析器定义、与数据库交互的数据上下文，我们可能有三个应用程序:

*   网
*   graphql
*   数据

鉴于伞状应用程序的神奇之处，我们仍然可以将它作为单个项目进行部署，并在配置和测试方面获得一些小小的便利。当请求进来时，web 应用程序处理它，调用 graphql 应用程序中的解析器函数，该函数又从 data 应用程序中检索一些数据。依赖关系是一个单一的顺序:web -> graphql -> data，所以当你编译它的时候，从最内部的依赖关系开始，一直回溯，正如你所期望的——但是如果所有的东西都部署在一起，你仍然可以访问技术上是循环依赖的模块，这打破了分离的概念。

### 这个应用有什么问题？

这种架构的主要问题是应用程序并没有因为正确的原因而被拆分。在一个不断增长的应用程序中(就随时间增加的代码而言),随着边界变得越来越脆弱和模糊，你增加的代码越多，你的速度就越慢。
产生这种效果的原因是，伞状子应用程序旨在作为一种方式被创建，**分别部署**它们中的每一个，因此是单独的配置和混合项目。因此，除非你单独部署应用程序，否则使用伞式应用程序没有任何好处。

也许有一天你需要这样做，但我可以保证进入伞状应用配置，在现有应用上改造比合并子应用更容易。

> 伞状子应用程序旨在被创建为一种分别部署它们的方式

### 更好的选择

我并不是提倡永远不要使用伞形应用，但我认为在大多数情况下，除非你有单独部署子应用的需求，否则最好不要使用。

另一种方法是使用“mix new”创建一个好的旧的 elixir 应用程序，并将所有的应用程序放入它们自己的文件夹中。你仍然可以在不使用伞状应用程序的情况下完成同样的架构，作为额外的奖励，你将能够快速迭代和改变你的决定，因为你了解更多关于你的业务领域，也许还有灵丹妙药！
这是一个更容易开始使用 Elixir 的方法，事实上 [Phoenix 推荐通过上下文以这种方式](https://hexdocs.pm/phoenix/contexts.html)构建你的应用。

我对伞式应用的体验主要是试图降低它的复杂性，喜欢模块多于应用。

事实上，在 [Elixir 的官方文档](https://elixir-lang.org/getting-started/mix-otp/dependencies-and-umbrella-projects.html#dont-drink-the-kool-aid)中，它解释了使用伞形应用的一些好处，并声明了一条免责声明:

> 虽然它在应用程序之间提供了一定程度的分离，但这些应用程序并没有完全分离，因为它们被假定为共享相同的配置和相同的依赖关系。

也许你的经历已经不一样了？如果有人发现了伞应用的成功，我很乐意讨论它！

最初发表于[jackmarchant.com](https://jackmarchant.com)