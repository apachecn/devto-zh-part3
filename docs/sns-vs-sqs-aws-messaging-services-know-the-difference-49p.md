# SNS vs SQS？AWS 消息服务-了解区别

> 原文：<https://dev.to/exampro/sns-vs-sqs-aws-messaging-services-know-the-difference-49p>

我注意到[海伦](https://dev.to/helenanders26)在社交网站和 SQS 上发布了这个好的开发工具，我碰巧有一个同样性质的视频，我想我应该上传它，从不同的角度提供一个解释。

## 文章不再可用

### 关于消息服务的更多信息

谈到**应用程序集成**，您需要知道的 4 项最重要的服务如下:

*   SNS - PubSub(思考传递)
*   SQS 排队(想想批处理)
*   Kinesis -实时
*   SWF -排队，但有人群

对于解决方案架构师助理和认证云从业者，您确实需要了解产品升级和技术支持服务与 SQS 之间的区别。

### MKS -亚马逊为卡夫卡管理流媒体

[![](img/14bf8846f14af617a5d379510c679829.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zZuNMlnn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fqvj5f39x1uyc8aopmmv.png)

BigData 中大多数希望使用实时消息系统的公司倾向于使用 Kafka。幸运的是，AWS 有一个名为 MKS(托管 Kafka 服务)的托管服务。

### 身势

[![](img/833440650f9ff828d455c17025e42991.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z9_OxEH2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ureov4khv5rygzy4gicu.png)

Kinesis 的用例非常适合需要简单流队列的游戏。有一个解决方案架构师专业考试问题，问你如何从物联网游戏设备中获取数百万个事件，然后插入到 DynamoDB 等数据库中以更新记分牌，因此 Kinesis 是你应该记住的。

在 ExamPro，我们建立了自己的实时事件跟踪(想想 GoogleAnalytics ),这样我们就可以准确地跟踪和改善人们在我们的平台上学习的方式。

#### SNS -亚马逊简单通知服务

[![](img/eb0df83918f1a9ab3d516690cf102a28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OOWtWcKN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/755hvaa221tqj4o5kcc7.png)

SNS 常用于发送简单的内部邮件。例如，当有人在 ExamPro 上注册时，我使用 AWS SDK 向 SNS 主题发送一条消息，然后它会向我发送一封纯文本电子邮件。

我们使用 SNS 的另一种方式是在你的应用中触发 webhooks。例如，假设您有一个 web 应用程序需要从 PDF 中提取数据，因此您需要使用 PDFTK，由于传统原因，您必须在 Lambda 函数中的自定义运行时运行该 PDFTK。当 lambda 处理完 pdf 后，你要通知你的 web 应用程序告诉用户它已经完成了。你可以让 Lambda 告诉 SNS，然后 SNS 会向你的 web 应用程序的 api 端点(webhook)发送一个 HTTP 请求，然后 web 应用程序会向用户更新 pdf 已经完成处理。

### SQS -亚马逊简单排队服务

[![](img/fb2216e8bca6bfd48b45217ba89197c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--noixX5PQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ybebqaquoyr18wgan9uv.png)

当我们在 ExamPro 上发送确认或重置密码等电子邮件时，我们使用 SQS 将电子邮件发送与我们的应用程序分离，因此不会导致应用程序挂起。ExamPro 是一个 Rails 应用程序，因此这个名为 Shoryuken 的 gem 使得开始与 SQS 威尔·利特尔知识一起工作变得非常容易。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[肖鲁肯](https://github.com/ruby-shoryuken/shoryuken)

### 一个超高效的基于亚马逊 SQS 线程的 Ruby 消息处理器

<article class="markdown-body entry-content container-lg" itemprop="text">

我正在寻找 Shoryuken 的维护者，你有兴趣帮助维护 Shoryuken 吗？[加入我们的懈怠](https://join.slack.com/t/shoryuken/shared_invite/zt-19xjq3iqc-KmoJ6eU6~qvZNqcLzIrjww)T3】

# 肖鲁肯

[![Shoryuken](img/c41b0cae55b5618148a7d0efe352cb2a.png)](https://github.com/ruby-shoryuken/shoryukenshoryuken.jpg)

Shoryuken *sho-ryu-ken* 是一个超高效的[亚马逊 SQS](https://aws.amazon.com/sqs/) 基于线程的消息处理器。

[![Build Status](img/89daf6a14a894ff27ad8563879044c11.png) ](https://github.com/ruby-shoryuken/shoryuken/actions) [ ![Code Climate](img/725d77c720e54d74b728c0fa644ad159.png)](https://codeclimate.com/github/phstc/shoryuken)

## 关键特征

*   [轨道活动作业](https://github.com/phstc/shoryuken/wiki/Rails-Integration-Active-Job)
*   [队列负载均衡](https://github.com/phstc/shoryuken/wiki/Shoryuken-options#load-balancing)
*   [每个队列的并发数](https://github.com/phstc/shoryuken/wiki/Processing-Groups)
*   [长轮询](https://github.com/phstc/shoryuken/wiki/Long-Polling)
*   [批处理](https://github.com/phstc/shoryuken/wiki/Worker-options#batch)
*   [自动扩展可见度超时](https://github.com/phstc/shoryuken/wiki/Worker-options#auto_visibility_timeout)
*   [指数补偿](https://github.com/phstc/shoryuken/wiki/Worker-options#retry_intervals)
*   [中间件支持](https://github.com/phstc/shoryuken/wiki/Middleware)
*   亚马逊 SQS CLI。参见`shoryuken help sqs`

## 要求

Ruby 2.4 或更高版本。

## 装置

将这一行添加到应用程序的 Gemfile 中:

```
gem 'shoryuken'
```

Enter fullscreen mode Exit fullscreen mode

如果您使用的是 AWS SDK 版本 3，也请添加这一行:

```
gem 'aws-sdk-sqs'
```

Enter fullscreen mode Exit fullscreen mode

为了保持 Shoryuken 与 AWS SDK 版本 2 和 3 兼容，需要额外的 gem `aws-sdk-sqs`。

然后执行:

```
$ bundle
```

Enter fullscreen mode Exit fullscreen mode

## 使用

查看[入门](https://github.com/phstc/shoryuken/wiki/Getting-Started)页面。

## 更多信息

更多信息请查看[维基页面](https://github.com/phstc/shoryuken/wiki)。

## 信用

[迈克·佩勒姆](https://github.com/mperham),[Sidekiq](https://github.com/mperham/sidekiq)的创作者，以及[所有为之做出贡献的人](https://github.com/mperham/sidekiq/graphs/contributors)。没有这些，Shoryuken 就不会存在

</article>

[View on GitHub](https://github.com/ruby-shoryuken/shoryuken)

### [swf 文件](#swf)

我知道没有人使用这项服务。我认为 AWS 使用它来帮助协调人和服务的任务序列，这对于亚马逊来说是有意义的，因为他们需要人将东西放入箱子，然后运输这些箱子。

甚至没有一个新的 AWS 图标。我一直不知道如何使用这项服务。它会在考试中表现出来。

### 帮我出制作更多免费内容

如果你喜欢这种 AWS 内容，你可以帮我的方法是将我添加到中的[链接，并认可我的 AWS 技能。不管怎样，你应该加我，我喜欢在 DEV.to 上与人交流](https://www.linkedin.com/in/andrew-wc-brown)