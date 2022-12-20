# 构建无服务器新闻文章监视器

> 原文：<https://dev.to/byrro/building-a-serverless-news-articles-monitor-a2o>

让我们自己的 API 以编程的方式监控新闻源会很方便。如果能够以结构化的格式提取新闻文章并添加元数据，那就更好了。

这正是我们所做的，并免费发布给你使用:[一个新闻文章监视器](https://github.com/byrro/serverless-news-monitor)，它运行在一个低成本且易于维护的无服务器基础设施上。

该应用由 Python [报纸库](https://github.com/codelucas/newspaper)提供支持，构建在[圣杯框架](https://github.com/aws/chalice)之上，在 [AWS Lambda](https://aws.amazon.com/lambda/) 上运行，由 [Dashbird](https://dashbird.io/) 监控，以确保安心。它是在麻省理工学院许可下发布的，所以你可以自由地使用它，并做任何你想做的修改。看一下[源代码](https://github.com/byrro/serverless-news-monitor)如果你想知道它是如何工作的，其实很简单。

## 快速演示

我们甚至发布了一个现场演示供您尝试。在您的浏览器中运行这些 URL 进行实验:

*   [TechCrunch 最新文章](https://vt7xjvnaw1.execute-api.us-east-1.amazonaws.com/Stage/build/techcrunch.com)
*   [摘录一篇黑客文章](https://vt7xjvnaw1.execute-api.us-east-1.amazonaws.com/Stage/parse-article/https%3A%2F%2Fhackernoon.com%2Fdebugging-serverless-applications-with-dashbird-82a2ed86d757)

## API 端点

API 基本上只有一个端点。它仅供演示使用。如果您计划定期和/或大规模使用它，我们恳请您派生/克隆[我们的存储库](https://github.com/byrro/serverless-news-monitor)并在您自己的 AWS 帐户中运行它。；)

*Obs。:此演示 API 具有适当的请求限制；请不要滥用它(3 个请求/分钟应该没问题)，否则它会暂时关闭 API 让别人尝试。我们的 Lambda 函数也被配置为在 5 秒内超时，并且一次只服务一个请求。*

`https://vt7xjvnaw1.execute-api.us-east-1.amazonaws.com/Stage/{action}/{param}/`

**`action`** 属性有三个可能的值:

*   **`build`** :收集给定新闻源的最新文章和附加数据
*   **`parse-article`** :解析并提取具体文章
*   **`get-meta`** :提供当前新闻环境状态的元数据

每个 **`action`** 在 **`param`** 属性中都期望自己的值:

*   **`build`** 和 **`parse-article`** 动作期望 URL 字符串为 **`param`**
*   **`get-meta`** 动作需要这些值中的一个或两个:`hot_topics`和/或`popular_urls`。要同时请求两者，请用逗号分隔:`hot_topics,popular_urls`。

## 提取数据

**`build`** 动作将返回关于新闻源的基本信息和他们的最新文章列表。 **`Get-meta`** 动作非常简单，提供一个热点关键词和新闻源 URL 列表。

**`Parse-article`** 更刺激一点。它将自动提取结构化的机器可读格式的文章数据:标题、出版日期、文本等。还提取了一些元数据，比如作者和图像。

最酷的是，报纸图书馆使用来自 [NLTK](https://www.nltk.org) 的 NLP 功能来基于原始文章生成内容:它总结文章文本并给出简短的几句话，以及识别最相关的关键词。例如，当您试图按主题聚合来自多个来源的文章时，这可能会很方便，或者可以使按主题索引文章变得更容易。

## 为什么无服务器

我们选择在 FaaS(功能即服务)基础架构上部署该应用程序，因为它极大地简化了基础架构的设置和管理。

根据您计划使用该应用程序提取的新闻来源和文章数量，传统的基于服务器的基础架构可能会很快变得难以维护。或者，如果你打算只是偶尔使用它，无服务器将为你节省很多钱，因为你不会为空闲时间支付一分钱。

通过使用 AWS Lambda，我们也有了一批 IP 地址可供使用。每个 Lambda 调用可以(不一定，但很可能)在一个具有不同 IP 地址的新容器上运行，这应该有助于分散任何给定新闻源的工作负载。

我们将我们的功能与 Dashbird.io 集成在一起，这样我们就可以让应用程序自动运行。Dashbird 扫描我们的日志，并通过电子邮件或 Slack 提醒我们，以防出现任何问题。它还采用异常检测算法来指出奇怪的行为或性能下降。由于这个演示的使用率很低，Dashbird 甚至免费做了所有的工作！

* * *

雷纳托·拜罗是 Dashbird 的开发者拥护者。你可以在[推特](https://twitter.com/ByrroRenato)上关注他。