# 语用追踪

> 原文：<https://dev.to/nmiculinic/pragmatic-tracing-cnf>

## 简介

`A distributed system is one in which the failure of a computer you didn't even know existed can render your own computer unusable - Leslie Lamport`

事实是，我们正在构建更大、更复杂、更分散的系统。曾经服务于旧式个人主页的单一服务器变成了以 medium.com 为中介的博客帖子和各种其他生态系统。简单的公告栏市场把亚马逊、易贝和其他在线零售商的庞大系统。这种进化类似于生命本身，当我们从单细胞细菌进化到今天的人类时，事情变得复杂了。更强大，但是复杂。

现在，我们来谈谈你目前的系统。机会不是一切都是新的。今天，几乎每个系统都使用几十年前用 Fortran 编写的数值例程。同样，在你的身体里，基本的细胞新陈代谢在过去的一亿年里没有改变。

尽管增加了复杂性，但有些事情确实发生了变化。除了优越的功能，如视觉、语言、意识，我们还开发了先进的监控设备。仅仅一个神经元和原生眼不能解决问题，但是一只成熟的眼睛可以。

庞大的神经网络几乎可以瞬间检测到你身体的问题，并向控制中心发出警报。想象一下，如果你不得不每一秒钟都检查一次，你是不是踢到脚趾了，还是在流血？

信息通过荷尔蒙在被称为循环系统(即血液)的巨大信息队列上发送和接收。比简单的单细胞生物和渗透扩散操作复杂得多。

因此，您的新系统无法像在低复杂性时代那样得到监控。日志已经不够了，指标、pprof 和类似的工具集也不够了。在本文中，我将跟踪作为您工具箱中的另一个工具，无论是在整体应用程序中还是在分布式设置中。对于特定用例来说，这是一个非常有用的工具。

## 可观察性工具集

在这一章中，我将简要介绍我们可以使用的基本可观察性工具。为了更清楚地解释，我还将使用足球作为比喻。对于熟悉的人，可以直接跳到最新的一节，跟踪。

### 日志

`log - an official record of events during the voyage of a ship or aircraft.`

这是我们可以使用的最简单和最基本的工具。一个日志代表发生的一些事件和相关的元数据。

最常见的元数据是事件发生的时间，以及哪个应用程序生成了该事件，在哪个主机上，该日志事件的级别是什么。

通常，日志事件级别是在系统日志级别之后建模的，调试、信息、警告、错误、关键是软件中使用最多的。

在应用程序和系统级别，通常有日志源和日志接收器。每个源生成日志数据，并将其发送到接收器。每个接收器可以应用一些过滤，例如仅错误级别或更高。
实际上，这意味着您正在向`stderr`写入日志级别或更高级别的信息，但是对于文件，您正在转储所有日志。

有用于收集和搜索日志的日志管理系统。其中比较简单的是带有 grep 的日志文件，比较复杂的是 systemd 中的日志记录，对于生产分布式系统，通常使用 ELK stack 或 graylog。

在足球的类比中，踢进球会是一个很棒的日志。大概是:

`msg="Ivan scored the goal" time="2018-01-12" game_minute="23"`

日志数据对于独特的、罕见的事件或有意义的事件非常有用。对于频繁发生的事件，需要对其进行采样，以免扼杀您的日志管理系统。例如，您是否希望每次在服务器上收到 IP 包时都进行记录？

### 度量标准

`metric - a system or standard of measurement.`

在可观测性上下文中，度量是随时间变化的标量值。该标量值通常是一个计数器(例如，得分目标数、HTTP 请求数)、量表(例如，温度、CPU 利用率)、直方图(例如，1xx、2xx、3xx、4xx、5xx HTTP 响应代码数)或等级估计(例如，95%百分比响应延迟)。

它们对于识别瓶颈、异常行为和设置 SLO(服务水平目标)非常有用。通常，警报与某个指标相关联，也就是说，每当指定的指标超出给定范围时，就执行一个操作——自动修复或通知操作员。

例如，对于人类，我们有一个非常重要的指标——血糖水平。对于健康人来说，过高的值会执行自动愈合操作，并向我们的血液中释放更多的胰岛素。另一个人类指标是你左腿的疼痛程度。通常，它接近 0，但超过某个阈值时，你会清楚地意识到它，即发出警报。

对于计算机系统，通常的指标与吞吐量、错误率、延迟、资源(CPU/GPU/网络/...)利用率。通常提到的系统有 statsd、graphite、grafana 和 Prometheus。

### pprov&火焰图

该工具最适合分析 CPU 密集型应用程序。在我解释它能为您提供什么之前，我想先介绍一下它是如何工作的。每秒 x 次，它停止你的程序并检查哪一行正在机器上执行。它将所有执行样本收集到每行/每个函数的桶中，然后重构哪个函数/行在哪个时间%被执行。就好像你正在拍摄足球比赛，看看每 10 秒钟谁有球，并由此重建谁拥有球的百分比。

### 痕迹

如果你从这篇博文中想起了什么，请记住这幅彗星图像:
[![commet](img/5793a6eaf3c533f186ea442d434394b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EU-oKaAz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.nature.com/polopoly_fs/7.15624.1392717237%21/image/Comet-ISON.jpg_gen/derivatives/landscape_630/Comet-ISON.jpg)

当它穿过地球大气层时，会在其路径上留下痕迹。从这个*痕迹*我们可以推断出它去过哪里，在那里呆了多长时间。在我们的程序中也有类似的情况。

跟踪表示请求/操作的单次执行。轨迹由多个跨度组成。Span 是系统中一个有意义的工作单元，例如数据库查询、RPC 调用、计算。范围可以是相关的，例如父/子范围。因此，trace 形成了一个生成树，或者更一般地说，一个 DAG(如果你引入了复杂的关系和其他花招)。

[![trace example](img/1d1ed58fd11e99ae967a4bd2df611521.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TrnF6j1T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/elghoujdami-labs/distributed-tracing/master/img/span-tarce.png)

每个 span 都可以附加有用的元数据——索引标记的键/值对，如 userId、http.statuscode、主机名或附加日志数据，如精确的数据库查询。跟踪后端提供了预期的搜索功能，按时间排序，按标签过滤等。

在足球的例子中，我们可以有一个代表得分的轨迹。它由 3 个跨度组成:

*   伊万在踢球
*   球滚向球门
*   球进入球门柱

## 常见用例

在这一节中，我将介绍跟踪的主要用例。与 pprof 等其他技术相比，跟踪可以检测程序何时由于 IO 等待、资源争用或其他原因而进入休眠状态。

### 总体要求概述

它允许您从单个痕迹进行案例研究。度量是聚合的，而 trace 关注的是单个请求。此人请求接触哪些服务？它在那里花了多少时间？在 CPU、GPU、网络调用等方面的时间细分是多少？

如果你正在调试你的应用程序，在你的痕迹中搜索特定的边缘情况，并对其进行分析。分析性能异常值也是如此。

### 大慢瓶颈

[![](img/bfeb27eb61db03564ff8e45f617c78c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ueboCPBe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/media-p.slid.es/uploads/7145img/5280805/Untitled_Diagram.svg)

当你有明显的瓶颈时，你知道你需要深入哪里。你已经把搜索范围缩小到这次行动了。

造成速度慢的瓶颈的原因可能是多方面的。也许你过度使用了锁，而程序正在等待锁。或者数据库查询优化不足/缺少索引。最后，您的算法适用于 10 个用户，但是在增长到 10 000 个用户后，它就太慢了。

找到它，观察它，分析它，修正它。

### 扇出

扇出是服务对每个传入请求发出的请求数。扇出越大，延迟越大。有时扇出是有意的，也是有用的，但是在这个上下文中，我们主要讨论的是在批量操作更适合的情况下反复调用相同的服务。

这就区别于:

```
SELECT id, name
FROM users
WHERE ... 
```

而不是遍历一些`id`列表并查询每个`id` :

```
SELECT name
FROM users
WHERE id = $1 AND ... 
```

这可能是无意中发生的，例如使用 ORM 框架决定做这件事。您已经获取了您的 id，现在您正愉快地循环访问它们，而不知道您每次都发出新的数据库查询。

其他时候是 API 设计的问题。如果您有用于获取用户数据的内部 API 端点，比如:`/api/v1/user/{id}`，但是需要批量导出，这也是同样的问题。

在跟踪过程中，您将看到对同一服务的许多小请求。尽管它们是并行的(尽管它们不是必须的)，你还是会遇到尾部延迟问题。

您将观察到调用服务的第 p 个百分点延迟的概率随着扇出度呈指数下降。这里有一个简单的图来说明它。

[![](img/4a5692a3d8dd6f25f874a1f0470e52c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vGU4x-Kf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ZJl2qx6.png)

## 铬描格式

[这个](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/preview)是用于单个进程跟踪的简单 JSON 格式规范。这个[弹射器](https://github.com/catapult-project/catapult)项目包括了这个规格的渲染。在 chrome 的`chrome://tracing/` URL 下有相同的渲染器。对于这种格式有各种各样的支持，比如 tensorflow 执行，golang 追踪，chrome 渲染本身等等。如果不需要分布式跟踪并且您的需求很简单，那么很容易将它包含到您的应用程序中。

例如，这个简单的文件:

```
[  {  "name":  "Asub",  "cat":  "PERF",  "ph":  "B",  "pid":  22630,  "tid":  22630,  "ts":  829  },  {  "name":  "Asub",  "cat":  "PERF",  "ph":  "E",  "pid":  22630,  "tid":  22630,  "ts":  833  }  ] 
```

呈现为:
[![chrome tracing rendering](img/e7858043b2e05c8f525d50c2ceffdb78.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cBrBzoym--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/f8oPz0h.png)

如果你对更多感兴趣，我建议至少浏览一下说明书。最大的缺点是使用可视化工具。作为一名新人，我很难找到如何在基本的滚动和查看之外，按类别、按名称和总体高级用例过滤数据点。

## 分布式追踪

在单个节点上一切都很好，但是麻烦从分布式系统开始。问题是如何连接/关联来自多个节点的轨迹。哪些跨度属于哪个轨迹，这些跨度是如何关联的？

今天，大多数解决方案都是从谷歌的短小精悍的论文中获取笔记。每个跟踪都有自己唯一的 traceID 和跨节点边界传播的每个 span 唯一 SpanID。

但是，对于如何传播这个上下文以及在传播过程中是否包括额外的数据(即行李)，有多种想法。此外，每个后端都有自己的想法，你应该如何交付跟踪/跨度数据给他们。

第一个可用的后端是 Zipkin，现在优步的 Jaeger 正在 CNCF 孵化项目，是一个很好的起点。此外，各种云提供商都有自己的内部 SaaS 跟踪(谷歌的 stackdriver，AWS X-Ray 等。)

下面是 Jaeger frontend 的一个截图，用于搜索和查看你的踪迹:
[![](img/2c36cb28b7489bf584ae1155aed5c5c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m8W9UbEa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://eng.uber.com/wp-content/uploads/2017/02/7-Screen-Shot-Search-Results.png)

由于针对特定后端的编写可能是一个硬供应商锁定，因此出现了两个客户端供应商中立的 API——open tracing 和 open census。

### 客户端厂商中立 API

在这一小节中，我将比较公开普查和公开追踪标准。两者都是不断发展的项目，GitHub start count 高，多语言支持，各种数据库中间件实现，HTTP 的 RPC，gRPC 等。

Open tracing 是目前 CNCF 正在孵化的项目，而 Open census 则是从 Google 的内部追踪工具中出现的。如今，open census 有自己的厂商中立组织。

至于特性集，open census 在其 API 中包含指标，而 open tracing 仅包含指标。

在开放普查中，您将追踪导出器添加到全局集合，而在开放追踪中，您必须在每次迭代中指定它们。他们有全局追踪器的概念，但至少在 go 中他们不提供默认，但你必须调用它。此外，与开放的人口普查相比，开放的追踪 API 感觉更笨拙，更不完善。

对于传播格式，公开普查指定标准。另一方面，开放跟踪只指定了 API，并且每个支持的后端都实现了标准的传播 API。

开放跟踪有一个包袱概念，即迫使一些数据沿着 span 传播上下文传播到每个下游服务。

### 打开人口普查示例

本小节将描述基本的开放式普查构件。完整的例子请看他们的官方文档，相当不错！他们甚至以伟大的[例子](https://github.com/census-instrumentation/opencensus-go/tree/master/examples)为特色。我的例子在 go 中，但是它们支持多种其他语言。为了简洁起见，省略了 import 语句。

首先，我们开始定义跟踪导出器:

```
j, err := jaeger.NewExporter(jaeger.Options{
        Endpoint:    "http://localhost:14268",
        ServiceName: "opencensus-tracing",
})
trace.RegisterExporter(j) 
```

我们可以使用 Docker 轻松启动它。从[页](https://www.jaegertracing.io/docs/1.7/getting-started/)开始

```
docker run -d --name jaeger \
  -e COLLECTOR_ZIPKIN_HTTP_PORT=9411 \
  -p 5775:5775/udp \
  -p 6831:6831/udp \
  -p 6832:6832/udp \
  -p 5778:5778 \
  -p 16686:16686 \
  -p 14268:14268 \
  -p 9411:9411 \
  jaegertracing/all-in-one:1.7 
```

然后我们开始一些跨度:

```
ctx, span := trace.StartSpan(ctx, "some/useful/name")
defer span.End() 
```

`ctx`是标准库`context.context`的实例

我们可以将一些索引标记的键/值元数据附加到这个 span:

```
span.AddAttribute(trace.StringAttribute("key", "value")) 
```

开放跟踪实际上指定了一些标准化的键(例如 error，http.statuscode，...).如果可以的话，我建议你使用它们，尽管公开人口普查没有具体说明。

或者我们可以添加一些日志数据，即注释:

```
span.Annotate(nil, "some useful annotation")
span.Annotate(
    []trace.Attribute{trace.BoolAttribute("key", true)},
    "some useful log data",
) 
```

在我们决定使用 http 之后，让我们注入开放的普查中间件:

```
client = &http.Client{Transport: &ochttp.Transport{}}  // client
http.ListenAndServe(addr, &ochttp.Handler{Handler: handler}) //server 
```

对于客户端，我们只需在请求中包含正确的上下文:

```
req = req.WithContext(ctx)
resp, err := client.Do(req) 
```

而在服务器端，我们从请求中获取上下文，继续我们的生活。

```
func HandleXXX (w http.ResponseWriter, req *http.Request) {
    ctx := req.Context() 
    // ...
} 
```

这一章可以在 5 分钟内总结为开放式人口普查速成班/备忘单。

## 总结

总结这篇博文，我最推荐跟踪这些用例:

*   关于整体绩效概述的推理
*   检测大型慢速操作
*   扇出检测

在这里，我展示了各种工具，如 chrome 跟踪格式、开放式跟踪和开放式普查。其中，我推荐从开放普查开始，在 docker 容器中启动 jaeger。尽可能使用中间件。

最后，收获利益！了解跟踪如何为您带来益处，以及您如何最好地利用它来取得成功。

## 参考&链接

*   [我的网络营 2018 演讲](https://www.youtube.com/watch?v=kN9PjsDhH-I)
*   [Chrome tracing 格式规范](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/preview)
*   [https://www.jaegertracing.io/docs/](https://www.jaegertracing.io/docs/)
*   [https://github.com/catapult-project/catapult](https://github.com/catapult-project/catapult)
*   [https://medium . com/@ copy construct/monitoring-in-the-time-of-cloud-native-c 87 c 7 a5 BFA 3 e](https://medium.com/@copyconstruct/monitoring-in-the-time-of-cloud-native-c87c7a5bfa3e)
*   [https://opencensus.io/](https://opencensus.io/)
*   [http://opentracing.io/](http://opentracing.io/)
*   [GOTO 2016 我希望在将优步扩展到 1000 个服务之前我已经知道了什么 Matt Ranney:](https://www.youtube.com/watch?v=kb-m2fasdDY)
*   [如何不用 Gil Tene 测量延迟:](https://www.youtube.com/watch?v=lJ8ydIuPFeU)
*   [所以，你想跟踪你的分布式系统？来自多年实践经验的关键设计见解](http://www.pdl.cmu.edu/PDL-FTP/SelfStar/CMU-PDL-14-102.pdf)