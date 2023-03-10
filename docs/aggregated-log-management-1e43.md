# 聚合日志管理

> 原文：<https://dev.to/kbariotis/aggregated-log-management-1e43>

我已经在我所有的项目中构建了聚合日志机制，并且我一直认为这是一种行业标准实践。最近我注意到事实并非如此，很多人都没有认真对待。

这篇文章试图收集我对这个问题的想法，希望能帮助其他人理解它。它来了。

## 为什么要测井

通常运行 web 应用程序的 OS 进程总是绑定到标准输出。可以是文件、终端或由主机提供商制作的 GUI。不管那是什么，我通常倾向于向我的函数抛出一堆日志语句，以便能够在以后跟踪它们的执行轨迹。

将这些跟踪与元数据打包在一起也很有用。昨天，特定用户发起了密码重置请求，或者特定待办事项的**创建失败。希望我能够追溯这些请求并看到更多信息。**

假设对于密码重置请求，我们将得到类似这样的结果:

```
82c838eb-736a-447b-b9e1-10dfe2bbe575 - Tue, 04 Dec 2018 20:27:16 GMT - POST /user/resetPassword - Initiated
82c838eb-736a-447b-b9e1-10dfe2bbe575 - Tue, 04 Dec 2018 20:27:16 GMT - User 145123 requested to reset password
82c838eb-736a-447b-b9e1-10dfe2bbe575 - Tue, 04 Dec 2018 20:27:16 GMT - User found, resetting password
82c838eb-736a-447b-b9e1-10dfe2bbe575 - Tue, 04 Dec 2018 20:27:16 GMT - Password reset
82c838eb-736a-447b-b9e1-10dfe2bbe575 - Tue, 04 Dec 2018 20:27:16 GMT - Attempt to send email
82c838eb-736a-447b-b9e1-10dfe2bbe575 - Tue, 04 Dec 2018 20:27:16 GMT - Email sent
82c838eb-736a-447b-b9e1-10dfe2bbe575 - Tue, 04 Dec 2018 20:27:16 GMT - POST /user/resetPassword - 200 OK 
```

以上是一个简化的示例，但是您可以看到我们已经有的详细粒度。我们可以很容易地发现进程中抛出的异常，找出系统行为的确切时间和原因。

但是上述内容会很快导致大量的日志，尤其是在高流量的环境中。幸运的是，有一些工具可以帮助我们更容易地找到我们需要的信息。

## 为什么要聚合

通过聚集这些日志，我们暗示有不止一个过程或者甚至完全独立的系统将生成日志。事实上，即使是最小的 web 应用程序也会由不同的部分组成，比如我们的应用程序的几个实例，它们公开了一个 REST API、数据库，可能还有一个负载平衡器。与其在不同的地方检查日志，不如在同一个地方聚集所有这些日志，并能够一起遍历它们。

更好的是，我们可以跨不同的系统跟踪执行，查看任何类型的日志或请求过程中可能出现的错误。我们的代码可能都是有效的，但我们可能忘记在数据库中添加一个索引。

## 麋鹿模型

正如这个行业中的每一个绝妙的想法一样，ELK 努力实现关注点的分离。ELK 代表 **E** lasticSearch、 **L** ogstash、 **K** ibana。它将这三种工具结合起来，以构建一个强大的日志聚合系统。

ElasticSearch 在引擎盖下使用 Lucene。它将存储和索引我们的日志，以便我们能够在其中进行搜索。Logstash(现在的 Filebeat)负责将我们系统的标准输出与 ElasticSearch 连接起来，最后，Kibana 是一个 GUI，它将帮助我们遍历日志并创建图表和可视化。

## 自主持

ELK stack 是开源的，可以由你自己托管。与所有自托管软件一样，尽管这意味着您可能需要监控 ES 集群并定期更新 Kibana。

AWS 提供了一个托管服务来创建 es 集群，其中的 [Kibana 作为插件](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-kibana.html)安装。你也可以在一个 ECS 任务中自我托管 Kibana 你的自我，以获得更多的自由。

## 第三方解决方案

papertrail 是一款出色的日志管理工具，可以将您的日志集中在一个地方。与 ELK 相比，papertrail 只是一个简单的终端，您可以在其中搜索文本。它不提供 ELK 的分析和可视化功能。

类似于 ELK，提供了它所有的功能，还增加了一些功能，比如闹钟。当您需要得到通知时，例如当某个事件发生时，或者当计算字段的数量超过某个阈值时，警报会很方便。

[Cloudwatch](https://aws.amazon.com/cloudwatch/) 是 AWS 在云中管理日志的原生解决方案。例如，如果您使用 EC2，您将能够在 Cloudwatch 中找到每个实例的日志。从那里你可以搜索它们，在特定的文本事件上创建触发器，甚至将它们重定向到其他服务。您可以将这些日志流式传输(使用 Lambda 函数)到一个基于 ELK 的外部堆栈，如 Loggly 或 AWS ElasticSearch 集群。

papertrail 和 Loggly 现在属于同一家公司，但我第一次被介绍给他们时，情况并非如此。我与这家公司没有任何关系。T3】

## 日志标准

为了索引服务能够更好地解析和分析您的日志，您必须能够提供标准的日志格式。没有每个人都使用的标准日志标准，但似乎每个案例都不一样。

被广泛使用的 Node.js 的 Bunyan logger[提供了一个很好的起点](https://github.com/trentm/node-bunyan#core-fields)。

```
{
  "name": "myserver",
  "hostname": "banana.local",
  "pid": 123,
  "req": {
    "method": "GET",
    "url": "/path?q=1#anchor",
    "headers": {
      "x-hi": "Mom",
      "connection": "close"
    },
    "remoteAddress": "120.0.0.1",
    "remotePort": 51244
  },
  "level": 3,
  "msg": "start request",
  "time": "2012-02-03T19:02:57.534Z",
  "v": 0
} 
```

ES 将对上述内容进行解析和索引，因此您可以对它们进行搜索和聚合。

想象一下，你早上醒来，看到一个客户的支持请求，他试图在你睡觉的时候使用你的服务，但是没有成功。哪里出了问题？您将能够通过搜索这些日志来回答这个问题，查看用户在崩溃前采取的路径，并最终找到错误和堆栈跟踪。

但这不是我们能从中获得的唯一有价值的信息。

## 分析学

通过存储这些信息，其他有趣的答案就出来了:

*   最常用的 API 路径是什么？
*   我们有没有收到任何可疑的邮件？
*   在组合中添加 HTTP 状态代码:我们得到了多少个 500？
*   在 mix 中添加用户名:审计特定的用户活动

*<small>小心你存储的用户识别信息。2018 年是 [GDPR 来](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation)实践的一年。通过存储(甚至更糟，向第三方服务提供信息，如 Loggly)用户身份信息，您违反了 GDPR 法律。</small>*

看看您如何回答 HTTP 流量、系统执行甚至业务特定操作的基本问题(甚至更复杂的问题)。

## 结论

我认为日志管理是每个软件架构的基础。我总是设计它，并让它尽早上市。

随着体系结构的发展，日志管理可能是一项单调乏味的任务。下面是对 StackOverflow 的监控过程的一些见解。但在此之前，中小型架构应该有一种简单的方法:

*   追溯单个请求的执行时间
*   分析并回答关于你的建筑的基本问题