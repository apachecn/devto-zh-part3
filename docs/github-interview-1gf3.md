# GitHub 采访

> 原文：<https://dev.to/m1guelpf/github-interview-1gf3>

[https://app.blogcast.host/embed/294](https://app.blogcast.host/embed/294)

> 去年 GitHub 联系我做了一个关于 UnMarkDocs 和我如何使用他们的 API 来构建它的采访。他们最终没有出版它，所以我想它会成为一篇短文的好素材。

### 你能介绍一下自己吗？你是哪里人？

👋🏻嗨！我是米格尔·彼德拉菲塔，16 岁，来自西班牙。

### 你有什么故事？

我一直被电脑所吸引，所以当我在学校被老师介绍 HTML 时，我知道那是我想做的。我开始自学，先是 HTML & CSS，然后是 Javascript，PHP 等等。

在几个测试项目之后，我构建了我的第一个真实的东西，一个为 GitHub 组织设计的邀请系统，名为 [OrgManager](https://github.com/orgmanager/orgmanager) 。我一直在用 GitHub API 尝试更多的小项目。 [UnMarkDocs](https://unmarkdocs.co) 和 [Snaptier](https://snaptier.co) 是那些实验的一些结果。

### 能否介绍一下 UnMarkDocs？它是做什么的？

UnMarkDocs 将存储在 GitHub 存储库中的原始 Markdown 文件转换成漂亮的文档页面。它还提供了一些扩展，因此你可以使用 Markdown 来添加提醒、行动号召、嵌入视频、代码笔、gists 等。最后，它确保存储库中的更改与您的页面同步。

### UnMarkDocs 背后有什么故事？你为什么建造它？

UnMarkDocs 最初是两个不同的项目。我当时在学 CSS，做了一个文档页面的 UI 工具包。我想把它用于我的开源项目的文档，所以我试着把它和 GitHub API 连接起来，利用 Contents API 的强大功能来呈现文档，而不用在我的服务器上克隆它。

在我做了一个演示之后，我决定继续做下去，并在我的 markdown 解析器上构建了一些扩展，允许嵌入视频、代码笔和其他我认为对文档页面有用的东西。

### 你最喜欢 GitHub API 的什么特性？

肯定是内容 API。它允许使用 GitHub 上的存储库做很多事情，而不必将文件实际存储在服务器上，这对于每月支付 500 美元购买服务器的人来说非常有帮助。这个端点支持我用 GitHub API 构建的大多数应用程序。

### 使用 API 构建 UnMarkDocs 最大的挑战是什么？

使存储库上的更改立即反映在文档页面上。我希望总是有最新版本的内容，但我也希望页面加载速度快。我的解决方案是缓存内容，并使用 webhooks 和 GitHub 应用程序在推送时清除缓存。

### 你使用了新的 v4 GraphQL API 吗？为什么或为什么不？

我使用 GraphQL API 通过获取文件树来自动生成导航。它使我不必在每个页面渲染中多次调用 GitHub API，还允许我通过使用片段来获得更深的树。

最初，我使用了一个单独的 OAuth 应用程序，但是当 GitHub 团队将 GraphQL 添加到 GitHub 应用程序中时(谢谢！)，我整合在主 app 上了。

### 关于你的经历，还有什么想分享的吗？通过构建 UnMarkDocs 有什么故事或经验教训吗？

在创建非马克文档的过程中，我学到了很多东西。在技术方面，我学到了扩展降价，设计，更多的 CSS，响应...但是我也从企业家的角度学到了很多关于市场营销、验证以及如何建立一个 T2 SaaS T3 的知识。