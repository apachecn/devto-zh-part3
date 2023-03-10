# 在深度嵌套的对象上发布 strapi @ 3 . 0 . 0-alpha 26-Filter

> 原文：<https://dev.to/strapijs/announcing-strapi-3-0-0-alpha-26-filter-on-deeply-nested-objects-57b3>

坐下来，放松一下，看看这个版本中会有什么。我非常高兴地宣布这一版本，因为它将是 Strapi 的最终 alpha 版本。是的，下一个将是第一个测试版！
这就是我们停止合并公关的原因，你可能在 [GitHub](https://github.com/strapi/strapi/blob/master/CONTRIBUTING.md) 上看到了。我们目前正在为管理面板和插件的新架构努力工作，以提供更好的 Strapi 核心稳定性。
因此，在接下来的 5 周内，我们不会合并任何公关，因为我们正专注于交付 Strapi 的测试版。最重要的是，你必须根据新的架构重写你的 PR，所以现在没有必要。

但是你还是可以贡献的！请参阅本文末尾的无需编码也能参与进来的内容。现在，让我们看看这个版本有什么新功能！

### 🔻过滤深度嵌套的对象

首先，深度过滤。你曾经想过滤关系的字段吗？现在你可以了🤩！这个新特性有几种版本:REST、GraphQL，目前所有的数据库都支持它(Mongo、SQLite、MySQL 和 PostgresQL)。

以下是一些例子:

[![GraphQL](img/6b03af8c380458b095e264f12bd9db77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yNxr2SwT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/41nrblxnp9bxm5zh1swi.png)
[![REST](img/d74b1d5b0ea7e0dc6279815dc087d35a.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--Aqmcbn_U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/llcnymm24qyt6ahm3sfz.jpg)

这项新功能还引入了一些我们认为您会喜欢的变化:

##### 多字段排序

`GET /products?_sort=created_at:desc,price:asc`

##### 多操作员过滤

`GET /products?updated_at_gt=2019-01-01T00:00:00&updated_at_lt=2019-01-10T00:00:00`

##### 查询数组创建或过滤

`GET /products?title_contains=ice&title_contains=fire`

##### 新建过滤器

最后，我们引入了两种新的过滤器:

*   `n_contains`:测试字段不包含值(不区分大小写)
*   `n_containss`:测试字段不包含值(不区分大小写)

这些新功能是由 [@alexandrebodin](https://twitter.com/_alexandrebodin) 和 [@kamalbennani](https://github.com/kamalbennani) 实现的！

### 🤸‍♀️稳定性提高

最近，我们的目标是稳定代码，为能够开发新功能而不回归打下基础，并测试、测试和再测试代码。在 beta 测试之前的这个阶段，这一点尤为重要，以便将来能够更快地发布。因此，GraphQL 已经被修复，其他一些 bug 也被杀死🔫：

*   【管理员】修复蓝色按钮([https://github.com/strapi/strapi/pull/3029](https://github.com/strapi/strapi/pull/3029))[@苏佩蒂](https://dev.to/soupette)*   [框架]修复前端生成器插件([https://github.com/strapi/strapi/pull/3030](https://github.com/strapi/strapi/pull/3030))[@苏佩蒂](https://dev.to/soupette)*   [框架]只在非 fork 回购上运行 snyk([https://github.com/strapi/strapi/pull/3010](https://github.com/strapi/strapi/pull/3010))[@ Alexandre bodin](https://dev.to/alexandrebodin)*   【插件】修复 GraphQL 上的大整数支持([https://github.com/strapi/strapi/pull/3090](https://github.com/strapi/strapi/pull/3090))@ derrickmehaffy*   [插件]测试版前修复的老问题([https://github.com/strapi/strapi/pull/3088](https://github.com/strapi/strapi/pull/3088))[@苏佩蒂](https://dev.to/soupette)*   [插件]在设置管理器中修复语言([https://github.com/strapi/strapi/pull/3038](https://github.com/strapi/strapi/pull/3038))[@苏佩蒂](https://dev.to/soupette)*   [Plugin] Trim content builder model attributes ([https://github.com/strapi/strapi/pull/2994](https://github.com/strapi/strapi/pull/2994)) @Getriax

    ### 📚文档——使用 PostgreSQL/MongoDB 在 Heroku 上部署

    你们中的许多人要求更详细的部署文档。对于英雄来说，现在已经完成了。
    你可以选择 MongoDB 或者 PostgresQL 作为数据库。

    [![Documentation Strapi for Heroku deployment](img/04d6f34ca558ce5fc4a6f5e2e66c2f70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w3RZp8YY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rgig9u58o5s4cyy5t0fp.gif)

    我们还根据我们的教程系列发布了一个视频:用 Strapi Headless CMS 学习 GatsbyJS。感谢 [@davidkartuzinski](https://twitter.com/kai_dawei) ，这段视频将向你展示[如何在 Heroku](https://www.youtube.com/watch?v=M1rEwMXK2z4) 上部署 Strapi。

    [![Learn GatsbyJS with Strapi Headless CMS Tutorial Series - Deploy Strapi to Heroku](img/e30759489de9e1ee61e6f438ad063e81.png)](https://youtu.be/M1rEwMXK2z4)

    ### 升级到[strapi @ 3 . 0 . 0-α26](mailto:strapi@3.0.0-alpha.26)

    如果您已经在使用 Strapi 构建 API，我们建议您迁移到这个版本。全面详细的[移民指南](https://strapi.io/documentation/3.x.x/migration-guide/migration-guide-alpha.25-to-alpha.26.html)已经发布。

    准备好了吗？
    只需使用一个命令行升级您的 Strapi 版本:

    ```
    npm i strapi@alpha -g 
    ```

    如果您是 Strapi 的新手，请尝试一下！

    看看[入门](https://strapi.io/documentation/3.x.x/getting-started/quick-start.html)指南，或者从下面的命令行开始:

    ```
    npm install strapi@alpha -g
    strapi new myproject --quickstart 
    ```

    ### 被牵连

    ##### 投稿！

    如果你以前从未参与过开源项目，那么参与其中可能会很可怕，而且不一定包括编码。

    > 即使你喜欢写代码，其他类型的贡献也是参与项目和结识其他社区成员的好方法。建立这些关系会让你有机会从事项目的其他部分。*[https://open source . guide](https://opensource.guide)T3】*

    例如，我们总是对来自开发者的相关客座博文开放，并分享杀手级内容！如果是你的案子，请告诉我们。
    您的贡献可以包括例如:

    *   开发技巧和诀窍:分享你是如何改进工作流程和优化 Strapi 使用的*   教程:通过分享如何实现一个特定的项目或功能来帮助社区*   Experience feedbacks: explain to new users what was your challenge and how Strapi helped you solve it

    如果你想谈论此事，请联系我们。

    如果你有问题或者只是想问声好👉我们总是很高兴在 [Twitter](https://www.twitter.com/strapijs) 、 [Github](https://www.github.com/strapi/strapi) 、 [Slack](https://slack.strapi.io) 或下面的评论中听到你的意见。请分享这篇文章，帮助更多人发现它。

    ##### 感谢社区

    Strapi 是 200 多名社区成员的产品。
    Strapi alpha.26 包含以下人员的代码贡献:
    [@kamalbennani](https://github.com/kamalbennani) 、 [@siddhartha-crypto](https://github.com/siddhartha-crypto) 、 [@MunGell](https://github.com/MunGell) 、 [@derrickmehaffy](https://github.com/derrickmehaffy) 、 [@abdonrd](https://github.com/abdonrd) 、 [@Getriax](https://github.com/Getriax) 以及 Strapi 团队成员 [@alexandrebodin](https://github.com/alexandrebodin) 、[@ aurelsicoko](https://github.com/aurelsicoko)

    ### 接下来是什么？

    在过去的几个月里，我们一直朝着一个目标努力:发布测试版。

    我们为自己设定的目标是:

    *   以下版本中的重大变更较少
    *   使用 Strapi 构建应用的更好体验
    *   更少的错误
    *   更好的团队工作流程
    *   部署将更加简单
    *   设置新的基础，为 Strapi 的管理带来定制

    请继续关注推特或[我们的博客](https://blog.strapi.io)。