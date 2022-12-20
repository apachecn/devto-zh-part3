# 使用 DynamoDB TTL 作为临时调度服务

> 原文：<https://dev.to/theburningmonk/using-dynamodb-ttl-as-an-ad-hoc-scheduling-service-2egg>

## 问题

在我的职业生涯中，我不得不多次实现调度特定任务的机制。通常，它们是一个更大系统的一部分。

例如:

*   游戏的锦标赛系统需要在锦标赛开始和结束时执行业务逻辑。

*   一个事件系统(想想 eventbrite.com 的 T1 或 meetup.com 的 T2 的 T3)需要一种机制来及时提醒参与者。

*   待办事项追踪器(想想[神童列表](https://www.wunderlist.com/))需要一种机制，在待办事项到期时发出提醒。

TL；DR 就是，我需要一种方法，在未来某个特定的时间点执行一段代码。几乎每种编程语言都有可能做到这一点。例如，。Net 有[定时器](https://docs.microsoft.com/en-us/dotnet/api/system.timers.timer?view=netframework-4.7.2)类，JavaScript 有 [setInterval](https://www.w3schools.com/jsref/met_win_setinterval.asp) 函数。但是我发现自己想要一个服务抽象来代替。遗憾的是，AWS 没有为这种类型的工作负载提供服务。 [CloudWatch 事件](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)是最接近的事情，但也不尽然。

## [T1】什么是 CloudWatch 事件？](#what-is-cloudwatch-events)

CloudWatch Events 提供了描述 AWS 资源变化的近乎实时的系统事件流。EC2 实例终止、ECS 任务启动、Lambda 函数创建等。

为了对这些系统事件做出反应，您可以为一个事件模式订阅一个 Lambda 函数。每当匹配到一个事件，CloudWatch Events 就会代表您调用目标 Lambda 函数。

[![](img/f8133c4bba96854ec81be1124abb2dd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y48og8_E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AZKZc5rH7czLAoKM87V2A3g.png)

此外，CloudWatch Events 还允许您轻松创建 cron 作业。

[![](img/ba873add202d4085a59e032130f8226b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t44WgW9---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A5G7sg0gdBevK4rgTWrYoMw.png)

这允许您基于固定的速率(低至每分钟)调用 Lambda 函数。或者，您可以使用 cron 表达式指定自定义计划。

## cloud watch 事件作为调度机制的问题

然而，CloudWatch Events 并不是为运行大量特定任务而设计的，每个任务都要在特定时间执行一次。

CloudWatch 事件的默认限制是每个地区每个帐户 100 条规则。这是一个软限额，因此可以请求提高限额。但是较低的初始限制表明它不是为需要调度**数百万个特别任务**的用例而设计的。

**CloudWatch Events 是为执行周期性任务**而设计的。

[![](img/f498d8a895bf4861b8b24c908d6c9421.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZvszXn80--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2Aec6t_1mE1zYAkknO.png)

因为没有其他合适的 AWS 服务，我不得不在职业生涯中自己实现几次调度服务。我尝试了许多不同的方法:

*   cron 作业(带有云观察事件)

*   包装。Net `Timer`类作为 HTTP 端点

*   使用 SQS 可见性超时来隐藏任务，直到它们到期

最近，我看到许多人使用 [DynamoDB 生存时间](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/howitworks-ttl.html) (TTL)来实现这些特定的任务。在这篇文章中，我们将看看这种方法，看看它在哪里适合你。

## 我们如何衡量方法？

对于这种类型的临时任务，我们通常关心:

**精度**:任务的执行离我的预定时间有多近？越近越好。

**Scale(未完成任务的数量)**:解决方案能否扩展以支持许多未完成的任务。即已调度但尚未执行的任务。

**扩展(热点)**:该解决方案是否可以扩展到同时执行多个任务？例如，数百万人设置了计时器来提醒自己观看超级碗比赛，所以所有的计时器都在临近开球时间时启动。

## DynamoDB TTL 作为调度机制

从高层次来看，这种方法如下所示:

[![](img/7f49b840d689ccb3dfb4a1100f0f22f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--glHiZMMw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AQmGmWhxrzcxysv8t.png)

*   一个`scheduled_items` DynamoDB 表，保存所有计划执行的任务。

*   将预定任务写入`scheduled_items`表的`scheduler`函数，TTL 设置为预定执行时间。

*   一个为`scheduled_items`订阅 DynamoDB 流并对`REMOVE`事件做出反应的`execute-on-schedule`函数。这些事件对应于从表中删除项目的时间。

### 可扩展性(打开任务的数量)

由于开放任务的数量只是转化为`scheduled_items`表中条目的数量，这种方法可以扩展到数百万个开放任务。

DynamoDB 也可以处理大吞吐量(数千 TPS)。因此，这种方法也可以应用于每秒调度数千个项目的场景。

### 可扩展性(热点)

当同时删除许多项时，它们只是在 DynamoDB 流中排队。AWS 还自动调整流中碎片的数量，因此随着吞吐量的增加，碎片的数量也会相应增加。

但是，事件是按顺序处理的。因此，您的函数可能需要一些时间来处理事件，具体取决于:

*   它在溪流中的位置，以及

*   处理每个事件需要多长时间。

因此，虽然这种方法可以扩展到支持许多同时到期的任务，但它不能保证任务按时执行。

### 精度

这是这种方法的一个大问题。根据官方[文档](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/howitworks-ttl.html)显示，过期的项目会在 48 小时内**删除。这是一个巨大的误差幅度！**

[![](img/4d0598047c1bdc136e7d8ca21fca2221.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Yy5cX8K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2A89a6zwwci_Why7da.png)

作为一个实验，我设置了一个阶跃函数状态机来:

1.  向`scheduled_items`表添加可配置数量的项目，TTL 在 1 至 10 分钟内到期

2.  跟踪任务被安排的时间和它被`execute-on-schedule`函数实际拾取的时间

3.  等待所有项目被删除

状态机看起来像这样:

[![](img/80fe9707f353bf3e73f43bd7b652089f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9xGAaOt5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2A7pzbiXJ52yCBWSV-.png)

我进行了几轮测试。无论表中有多少项，结果都是一致的。快速浏览一下表格，您会发现，平均而言，一个任务在预定时间之后的 11 分钟内被执行。

[![](img/eb14f9364c2c8529cf5b62beaf488908.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8FoU71jt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2A7dpnNYGDePArpz2H.png)

我在其他几个 AWS 地区重复了这个实验:

[![](img/28d57cbd5bd32b59baf72b0b35955d64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K_ONtpKH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AksQ9IWHV1RX_vYcA.png)

我不知道为什么 US-EAST-1 和其他地区有如此显著的差异。一种解释是，TTL 过程在表创建后需要一点时间来启动。因为我最初是针对 US-EAST-1 地区开发的，所以与其他地区相比，它的 TTL 进程已经“升温”了。

根据我的实验结果，使用 DynamoDB TTL 作为调度机制似乎不能保证合理的精度。

一方面，这种方法非常适用。但是另一方面，计划的任务至少在几分钟后执行，这使得它不适合许多用例。