# 2018 年，我们在 store2be 的堆栈中发生了什么变化——回顾展

> 原文：<https://dev.to/tomhoule/what-changed-in-our-stack-at-store2be-in-2018---a-retrospective-4h9>

随着我们的团队和代码库的增长，我们有时会发现有必要回顾一下事情是如何变化的，并深情地记住我们编写和删除的代码，我们构建和废弃的抽象，我们的代码过去是怎样的，以及将它带到现在的位置是多么有趣。

我们正在构建一个平台，让实时营销变得可扩展、可衡量和有针对性——因此，我们为平台的不同参与者提供了多个应用程序和一个 API。我们希望确保我们能够不断发展，使平台适应不断变化的需求，并在此过程中保持灵活性和稳定性。这是我们在对我们的技术堆栈做出决策时的高级思维模式。这篇文章总结了它在 2018 年对我们的影响。

[![store2be developer, early 2018](img/469ddceb4c168f2b805c09ade35e76fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---S_rBcfO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hpfe64rk6thzrz1qyuq8.jpg) 
*store2be 开发者，2018 年初(图片 Tambako The Jaguar，flickr)*

## Frontend-React 应用程序

我们在 2017 年底在 React 应用中引入了 Typescript，我们现在才接近完全类型化的前端应用。在 Typescript 之前，当我们尝试更可扩展的状态管理和异步操作方法时，我们的 JS 架构受到了冲击——我们在 [redux-saga](https://github.com/redux-saga/redux-saga) 之上有自己的抽象，甚至开源了我们处理 redux、 [redux-belt](https://github.com/store2be/redux-belt/) 中样板文件的方式。这些抽象是不断发展的，在不引入类型错误的情况下，在非常有状态的应用程序中发展核心抽象是一个挑战，类型错误会直接导致我们的用户崩溃。网上有足够多的 Typescript 成功案例，这个我就不做过多阐述了。

好的工具是我们欣赏的东西，所以除了 Typescript，我们还采用了更漂亮的，[t slint](https://github.com/palantir/tslint/)(除了 ESLint)，[jest](https://jestjs.io/)(替换 jasmine)，以及[快照测试](https://jestjs.io/docs/en/snapshot-testing.html)。所有这些都减轻了很多精神负担:不再担心或手动摆弄代码格式，自动执行约定，更确定 UI 不会意外改变，等等。林挺，测试和更多的东西在 CI 中被强制执行，所以我们可以在代码评审中关注困难的问题。

我们非常喜欢这种体验，我们用 typescript 实现了电子邮件模板的后端服务，[写了关于它的文章](https://tech.store2be.com/email/sendwithus/mjml/typescript/react/2018/06/14/email-templates-at-store2be-and-gdpr/)。

我们工具箱中另一个值得注意和备受赞赏的新增功能是用于我们共享 UI 组件的 [react-storybook](https://github.com/storybooks/storybook) 。

## 意想不到的旅程——graph QL 在我们的应用中

尽管前端工作流程发生了许多变化，架构问题仍然让我们的生活变得困难。

1.  我们的 RESTful API 结构没有太大的发展:我们的大多数端点直接映射到数据库表，几乎没有额外的逻辑。随着更多的资源和更复杂的业务逻辑，这意味着**许多业务逻辑必须在前端**实现，并且仅仅由 API 验证。
2.  **React 应用中的状态管理和同步变得过于复杂** -一些操作和查询需要多次网络调用和分派多个 redux 操作。一些关键操作不是原子性的，而它们应该是原子性的。
3.  **让新开发人员加入我们基于 redux-saga 的抽象**和所有相关约定**被证明是具有挑战性的**，因为文档并不像它应该的那样全面，而且新旧约定共存。
4.  由于我们的应用程序中的大部分数据来自 API，我们觉得为所有这些资源手动编写 Typescript 接口是一项容易出错的手工工作。我们想过从我们的 [OpenAPI/Swagger](https://swagger.io/specification/) 文档中生成它们，但是这个实验不是很有说服力。再加上 redux-saga 生成器的类型信息丢失，我们觉得我们**没有意识到 Typescript 的潜力**。
5.  **文档作为 Swagger 规范**手动维护。这也是大量容易出错的手动工作。

面对这些问题，我们决定尝试通过逐步引入一个新的 GraphQL API 来解决它们，这个 API 与现有的 RESTful API 在同一个 Rails 代码库中。以下是我们观察到的情况:

1.  额外的一层给了我们更多的空间和结构来将业务逻辑移回到后端。
2.  **前端的数据获取现在变得简单多了**，查询与视图或多或少地一对一映射，表单和按钮也发生了变化。我们不再对 CRUD 使用任何基于 redux 的抽象，状态管理代码的总量也减少了。状态也(或看起来)更加局限于它在组件树中的位置。我们发现这更容易理解。
3.  这种简单性，以及对一个被广泛采用并记录在案的数据获取标准的坚持，使得**入职变得更加容易**。对于前端开发者来说，有很多好的 GraphQL 资源([官方指南](https://graphql.org/learn/)，仅举一例)。根据我们的经验，掌握这项技术对每个人来说都很容易。
4.  这个工具很神奇，它让我们**通过为我们的 API 数据**生成精确的类型来利用 Typescript 的优势。我们使用 apollo-client 和 apollo-codegen，它们都有很好的文档记录，运行良好，缓存层是最棒的。
5.  我们的 API 文档现在有了更好的形式，因为大部分都是自动化的，保持它与实现同步是微不足道的，并且 [GraphiQL](https://github.com/graphql/graphiql) 提供了一个很好的界面来探索它。

我们花了一些时间来实现一个可行的最小实现，但是一旦部署完成，我们很快就明白了我们想要朝那个方向发展。

我们在迁移前没有发现的另外两个棘手问题，但最终得到了改善:

*   **可调试性**。 [GraphiQL](https://github.com/graphql/graphiql) 已经成为识别错误来源的福音。对于不容易再现的多个请求，有时很难区分后端和前端问题(网络工具不允许您轻松地调整和再现请求)。有了可以直接作为链接共享的查询——[GraphiQL](https://github.com/graphql/graphiql)可以从 URL 查询参数中获取查询——我们节省了大量调试时间。
*   **可进化性**。逐步添加和取消字段的方式极大地减少了必要的重大更改的频率，从而使部署更加顺畅。我们使用 [eslint-plugin-graphql](https://github.com/apollographql/eslint-plugin-graphql) 来警告我们一个应用程序正在使用一个已经被否决的字段。

[![store2be developer discovering GraphiQL](img/8ea43a4538aea449e8f1dfd0509aa073.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1PRNvkkG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hejwkjhf60nqknyagmyf.jpg) 
*store2be 开发者发现 GraphiQL(照片不详)*

## 我们的 Rails API 中的 GraphQL

回想起来，在前端采用 GraphQL 是一件轻而易举的事情。它确实需要一些改变，但朝着一个更标准的工作流程，有一系列工具，使我们的生活更容易。这在 API 中是不一样的。与纯 RESTful APIs 相比，GraphQL APIs 提出了一系列不同的问题，需要不同的解决方案。列举一些我们面临的挑战:

*   **代码组织**。我们很自然地从分离向用户公开的 API 和用于内部工具的 API 开始，因为我们有独立的端点集。这似乎是区分管理员和普通用户应该看到什么和能够做什么的最简单的方法，代码共享仍然是可能的。由于各种原因，这被证明是一个糟糕的决定，几个月后我们最终合并了这两个 API。这只是我们在 GraphQL API 中进行适当的代码组织和重用时遇到的困难的一个例子。我们现在有了一套很好的约定和抽象，但是还需要大量的探索。
*   **测试**。 [graphql-ruby](http://graphql-ruby.org/) 中的一些抽象对于测试来说不是很友好，所以我们必须想出一个测试友好的代码结构。
*   **限制访问和可见性**。这里没什么特别有挑战性的，但是这个也是需要学习的。
*   **正确使用更多“高级”功能，如界面**
*   **避免 n+1 查询问题**——有一个得到很好支持的、有良好文档记录的、简单明了的解决方案， [graphql-batch](https://github.com/Shopify/graphql-batch) (由 Shopify 提供)，但这是需要学习和实现的另一件事。

我们花了几个月的时间来确定令人满意的抽象概念。像 [Shopify GraphQL 设计教程](https://github.com/Shopify/graphql-design-tutorial)这样的资源很有帮助。总的来说，让具有相关知识或兴趣/经验的人倡导 GraphQL 良好实践和工具会有所帮助。

既然已经很好地定义了抽象，我们发现让开发人员使用 API 更容易了，而且我们更多地使用普通的 ruby 类，而不是模型和控制器中特定于 Rails 的方法。

简而言之，当最初的障碍过去后，这一改变受到了整个车队的热烈欢迎，我们对此感到非常高兴。

## 后端和基础设施-事件，k8s

我们开始为我们的平台实现一个事件日志，用于审计、调试以及将来的解耦任务。目前的主要功能是我们内部应用程序中的时间线，但它为 2019 年开辟了许多视角和挑战。

我们一直在做的另一个改变是向 Kubernetes 的迁移。我们严格遵守代码为的*基础设施，并将我们所有的基础设施定义为 terraform 脚本和舵图。我们希望今年完成迁移。途中我们发现了很多工具，但有一个有趣的工具值得一提，那就是[click](https://github.com/databricks/click)；它充当了 kubernetes 集群的一种 REPL/终端 UI。*

## 科技手册

在 2017 年底，我们的团队仍然处于这样一种规模，即每个开发人员都在他们的脑海中拥有相对完整的代码库知识，但这不能持续下去。我们开始并发展我们的内部开发团队文档，我们称之为技术手册。它采用 GitHub 存储库的形式，带有简单的 markdown 文件——带有索引和目录——因此编辑工作流程对每个人来说都很舒适。我们发现，随着代码库的增长，这不是一件好事，而是一件必需品。

## 签收

[![store2be developer, early 2019](img/ad87ca88a0bbbb85455889d883660126.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--9PbedXe8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dyms2tecc4eucy0d8vdi.jpg) * store2be 开发者，2019 年初(图片 HuffingtonPost)*

我们在 2018 年对我们的堆栈进行了相当大的改变，并且在这个过程中没有损害实际功能的交付。没有艰难的、突破性的改变、迁移或重写，只有改进。让我们希望 2019 年也是如此。

如果你想了解更多，请随时通过 [twitter](https://twitter.com/store2be_tech) 联系我们。

你好，我们是 store2be，这是一家位于柏林的初创公司，致力于为短期零售空间建立一个 SaaS 式的市场。如果您喜欢我们发布的内容，您可能想查看 store2be 技术页面或关注我们的媒体渠道。