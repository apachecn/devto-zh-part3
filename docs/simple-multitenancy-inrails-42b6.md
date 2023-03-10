# Rails 中的简单多租户

> 原文：<https://dev.to/ritikesh/simple-multitenancy-inrails-42b6>

在这篇文章中，我想分享一些关于如何使用 Rails 中的关注点来分享一些基本的可重用多租户模式的片段。

* * *

[多租户](https://en.wikipedia.org/wiki/Multitenancy)是当今云优先世界中需要解决的常见问题。多租户架构的核心部分是“租户”。租户可以是任何东西——商店(Shopify)、账户(使用 SaaS 产品的组织),甚至是用户——通常将数据划分到相关的表或数据库中。
对于繁重的多租户特性集或功能，有许多社区驱动的 gem 可用。你可以在 [ruby-toolbox](https://www.ruby-toolbox.com/categories/Multitenancy) 上找到完整的列表。对于较小的项目或学习练习，我宁愿建议您自己构建系统。它让您对框架本身有了更清晰的理解。
下面是一个简单的问题，可以帮助您开始设计多租户应用。默认情况下，包含以下问题的所有模型都将是多租户的。