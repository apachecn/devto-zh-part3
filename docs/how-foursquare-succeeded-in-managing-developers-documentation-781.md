# Foursquare 如何成功管理开发者的文档

> 原文：<https://dev.to/rob_renner/how-foursquare-succeeded-in-managing-developers-documentation-781>

建立一个强大的开发者社区最重要但经常被忽视的一个方面是文档。因此，看看那些大玩家是如何处理这些问题的总是一个很好的实践。

在这篇文章中，我将向你简要介绍一下 [Foursquare](https://foursquare.com/) 是如何成功处理这些事情的。

在审查了现有的文档后，他们推出了一个全新版本的[developer.foursquare.com](https://developer.foursquare.com/)，充满了*的功能，“帮助开发者开始并探索他们的位置智能工具”*。

在这个简短的概述中，我想涵盖两个方面:基础设施和特性改进。

# 基础设施改善

**✔内容管理** — [Jekyll](https://jekyllrb.com/) **框架**用于将 markdown 文档转换为可重用的文档平台，而**自定义插件**有助于更快更轻松地更新文档

**✔部署** —有一个**脚本**用于从 Dockerfile 构建一个图像，并通过 [Nginx](https://nginx.org/en/) web 服务器公开 Foursquare 的页面

**✔自动化** —为了分离文档并允许快速实时更新，**建筑**通过 [Travis-Ci](http://travis-ci.com/) 自动**拾取**

# 功能改进

**✔搜索**——four square 使用 [ElasticSearch](https://www.elastic.co/) 建立了一个搜索引擎，对每个 docs 站点更新进行重新索引。搜索引擎使开发人员能够找到特定的关键字、端点或错误响应。更重要的是，该公司已经创建了一个 API，允许无缝集成到第三方。因此，直接集成到像 Android Studio 和 Xcode 这样的 ide 是很容易的

**✔端点页面** —由于端点页面具有 API 响应，这绝对是一件好事，开发者可以很容易地看到端点的预期输出

**✔样本应用&库** —出现了额外的[样本应用](https://developer.foursquare.com/docs/sample-apps)。例如，有了它们，你可以找到最近的咖啡店，选择一个地方出去兜风，甚至通过增强现实探索现实世界。至于新的[客户端库](https://developer.foursquare.com/docs/api/libraries)，它们现在支持 Swift 和 Go

— — — — — — — — — — — — — — — — — — — — — — — — — — — — —

好吧，如果你还没用过 Foursquare 的 API，可以随意研究一下他们的 **[入门指南](https://developer.foursquare.com/docs/api/getting-started)** 。您将能够在 5 个简单的步骤中创建一个帐户并进行第一次 API 调用。

基于:Arin Ray 的[开发者文档新体验&资源](https://medium.com/foursquare-direct/a-new-experience-for-developer-documentation-resources-f753ee1ba51?fbclid=IwAR3uKULZ9NpzxJPCMp9vW_evyDTjatdMnV-ZaywearnzJ_b982kq8hEjYAU)。

[原帖](https://javashop.pl/how-foursquare-succeeded-in-managing-developers-documentation/)。