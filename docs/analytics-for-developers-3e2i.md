# 面向开发者的分析

> 原文：<https://dev.to/brendt/analytics-for-developers-3e2i>

我经营这个博客已经快三年了。我使用过谷歌分析，不仅跟踪用户数量，还积极改进我的博客。

我不是营销人员，只是一个简单的开发人员。今天，我想分享我的技术经验，我如何使用交通数据并对其做出反应。

## 过滤数据

首先也是最重要的，无聊的部分。它需要不到五分钟来设置，但会提高您的数据的正确性:过滤器。

如你所知，谷歌分析与跟踪代码一起工作，你可以把它放在任何地方。跟踪代码很容易被用在其他地方，污染你的数据。我想到的是本地开发环境，或者更恶意的是:窃取您的跟踪代码的人故意发起攻击。

幸运的是，这可以很容易地用过滤器来防止。因此，请花五分钟时间在管理部分设置一些过滤器:

[![](img/599c72669158192b6aad6c037a8c7868.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--44pK11NV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.stitcher.io/resources/img/blog/analytics/filter-1.png)

通过过滤器，您可以建立一个想要包含的流量白名单。我通常会创建一个名为“过滤”的单独视图，并保持“所有网站数据”视图不变，以供测试。

下面是我设置的一个过滤器示例，仅包含来自“stitcher.io”域的流量:

[![](img/61132f00e2c93dce32b9c5cdf91173bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kPH_SI6M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.stitcher.io/resources/img/blog/analytics/filter-2.png)

你也可以过滤掉本地域，白名单 IP 地址等等。如果你想真正安全，你可以把你的主机的 IP 地址和主机名一起加入白名单。

设置好正确的过滤器后，就该解释数据了。

## 递减跳出率

这里有一个好问题:“我应该降低我的跳出率吗？”企业营销人员当然会大声说“是”。

继续阅读 https://stitcher.io/blog/analytics-for-developers