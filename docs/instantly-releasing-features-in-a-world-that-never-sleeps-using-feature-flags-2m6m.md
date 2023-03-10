# 使用功能标志，在一个不眠的世界中即时发布功能

> 原文：<https://dev.to/claudiosanchez/instantly-releasing-features-in-a-world-that-never-sleeps-using-feature-flags-2m6m>

[![Related image](img/de95a35323d5516d3b327ddf93c48013.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U69z9Rdx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AHROSl2j8Ib7keDGF6_8PTQ.jpeg)

现在，企业比以往任何时候都更需要保持敏捷，并继续以光速提供各种功能。在竞争激烈的移动应用领域，这一点更是如此。

我们可能要等几个月才能发布新版本应用程序的日子已经一去不复返了。

如今，客户希望你的移动应用能像他们的脸书应用、银行应用或约会应用一样灵活。如果你不愿意做，竞争对手会做。

正如你可能知道的(希望通过别人的痛苦)，推出一个有缺陷的功能是非常危险的，因为在应用程序商店的世界里，一个新版本可能需要 [1 到 2 天才能获得批准](https://developer.apple.com/app-store/review/)，这可能会让你的客户对你的产品永远失去信心。

那么，如何才能做到两全其美呢？不断贪得无厌的新功能的渴望，同时仍然服务于永远工作永不回滚的主？

[特色旗](https://martinfowler.com/articles/feature-toggles.html)，朋友！它们是构成梦想的材料。

> “通过功能标志，您可以实时推出全新的功能和体验。您不必对每个版本中的每个功能都全力以赴，这样可以消除很多风险，如果功能需要更多的工作，您可以立即回滚。”
> 
> –Apptimize 首席执行官 Nancy Hua。

现在，让我们来谈谈如何通过利用[launch darky](https://launchdarkly.com/)在我们的 Xamarin 移动应用程序中即时发布功能，将功能标记为服务。

比方说，我们的移动银行应用团队正在推出一个非常创新的功能，叫做**消费分析**。这项功能可以跟踪一段时间内的支出和收入，并告诉客户他们还有多少钱可以花。现在，我们的数据科学部门仍在调整算法，因此他们希望确保该功能可以推广到他们的一部分客户，并最终推广到整个消费者群。

[![](img/b5370be1428d129e3711afd50db30731.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YPQ07HXa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMWFRlEe13Ih56FJt0faq8A.png) 

<figcaption>这是我们打开功能后的最终结果。</figcaption>

launch crystally 网站有一个非常简单的注册流程和[入职](https://app.launchdarkly.com/signup)。这样做花了我们大约 20 秒，我们进去了。该网站让你选择做一个快速启动，或直接跳过仪表板；我们选择了后者。

为我们的移动应用程序创建一个功能标志很简单。我们给了它一个名字和密钥。我们还选择了一个**布尔**作为标志的种类。最后，我们选中了复选框*使该标志对客户端 SDK 可用，*以便以后将该标志提供给我们的移动应用程序。

[![](img/82edb61b2e52595cada10f0eaa0b866f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rC1T4acM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHxBJ6DNcBD7jwiPxC7tzgQ.png)

在创建了几个特性标志之后，我们差不多就可以开始工作了。

[![](img/26f3aea6c3c127ff984e5a8498b9c647.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ugYkwM22--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AqCaQAlhw2xcsQY2ukeM6-A.png) 

<figcaption>黑屏仪表盘</figcaption>

然后，我们转移到故事的客户端，实现支出分析，并放在移动应用程序的主页上。

[![](img/6890706921652b5a9e1386b614b395ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RMxj57rH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/752/1%2AxYLJ_Z6bWQci4bb4iKwCug.png) 

<figcaption>支出分析小工具</figcaption>

让我们将 LaunchDarkly Xamarin SDK 添加到我们的项目中，包括[launch darylly。xa marin](https://github.com/launchdarkly/xamarin-client)n 将包裹发送到您的。NET 标准项目。 ***在写*** 的时候，NuGet 包还在**预发布**，版本 1.0.0-beta9。还有一种更成熟的。NET 客户端，但由于我们喜欢生活在边缘，让我们坚持测试版。

[![](img/275d030273eaa853d4b29be53e6c3fa5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5oz_CZYM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASFBTevUgZ9xoH7ep5nRF9g.png)T3】

```
Install-Package LaunchDarkly.Xamarin 
```

在应用程序初始化过程中的某个方便的时候，您可能想要初始化 launch crystally 客户端。

到目前为止，您应该已经熟悉了这些移动 SDK 的范例。你创建了一个项目，给了我们某种类型的*密钥*，作为一种身份验证的形式。我们将它存储在一个名为 MOBILE_KEY_TEST 的字段中。