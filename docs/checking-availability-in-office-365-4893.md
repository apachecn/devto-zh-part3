# 在 Office 365 中检查可用性

> 原文：<https://dev.to/psyked/checking-availability-in-office-365-4893>

#### 如何使用(beta) Office 365 Graph API 检查会议室可用性。

> 虽然看起来很奇怪，但这并不是显而易见的事情，所以请允许我为您节省半个小时的搜索时间，并直接给你答案。

<figure>[![](img/c7ebdc987f866f2122612b8499146da7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dyQGDcpr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxxKgPqB6Hkmx6y3gJS8LwA.jpeg) 

<figcaption>照片由 [rawpixel](https://unsplash.com/photos/mcLpPD36-2k?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Unsplash](https://unsplash.com/search/photos/meeting?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

### 背景

在办公室，我们有一系列虚拟和物理会议室，但尽管现成的工具很好，但在短时间内找到一个可用的房间仍然是一项艰巨的任务。如果能够利用一些 API，并且能够构建我们自己的定制工具来使可用性更加…可见，那就太好了。

### 答案；又名。如何做:

1.  首先，获取一个 API 访问令牌。
2.  使用 JSON 有效负载创建一个 Graph API 请求，概述目标电子邮件地址和要检查的时间段。
3.  将响应解析成可用的数据。

#### 获取访问令牌

在开始之前，您需要一个*访问令牌*，您可以通过遵循[官方文档](https://docs.microsoft.com/en-us/graph/auth-overview)中的步骤来获得它，或者为了使事情更简单，您可以使用图形资源管理器工具来尝试请求:

[图形浏览器-微软图形](https://developer.microsoft.com/graph/graph-explorer/)

#### 制作图形 API 请求

向/me/calendar/getschedule 图形 API 端点发出 POST 请求:[https://graph.microsoft.com/beta/me/calendar/getschedule](https://graph.microsoft.com/beta/me/calendar/getschedule)——目前仅在测试版中可用——使用类似如下的 JSON 请求体: