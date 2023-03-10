# 日期时间。现在:不，不...

> 原文：<https://dev.to/lbugnion/datetime-now-no-no-1013>

最近，我的同事兼朋友多诺万·布朗(Donovan Brown)在瑞士拜访客户，并参加在苏黎世举行的互联网日会议。我利用这个机会和他呆了一会儿，我们聊了聊(如你所料)令人讨厌的和“令人不快”的东西。

讨论的一个话题(其中一个客户也提到了)是:您需要为云做什么准备...其中一个主题是:时区！！

# 到底几点了？

因为我们拥有所有的区域，这意味着您的代码突然有了轻松走向全球的潜力:)所以您可以做一个有趣的实验:

1.  创建新的 web 应用程序。这里我使用 Visual Studio 创建了一个带有 Razor pages 的[ASP.NET 核心 web 应用程序。](http://gslb.ch/d286b)

2.  在 HTML 代码中，显示本地时间和 UTC 时间。在 ASP.NET，我可以在 index.cshtml 中使用下面的代码:

```
<h1>Local time: @DateTime.Now</h1>

<h1>UTC: @DateTime.UtcNow</h1> 
```

Enter fullscreen mode Exit fullscreen mode

> 在 Razor 页面中，可以直接从 CSHTML 标记中调用 C#代码，非常方便。

1.  在本地运行应用程序。如果你像我一样生活在西欧，你应该会看到类似下面的*(注意当地时间和 UTC 之间的 2 小时时差)*。如果您在其他地方工作，时间会有所不同，但是本地时间和 UTC 时间很有可能会不同(当然，如果您碰巧处于本地时间与 UTC 时间相同的时区，情况就不同了)。

[![Running the application locally](img/5450fc4be29f8d0d246387cb9e12cae4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RoW6jlWH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4tpyWmC.png)

1.  现在将您的应用程序部署到 Azure，部署到美国西部地区。你可以直接从 Visual Studio 中完成，或者在 [Azure 门户](http://portal.azure.com)中创建一个应用服务并部署在那里。

2.  运行 West US 应用程序。现在，您应该看到本地时间和 UTC 时间显示相同。

[![Running the application in Azure](img/c7ee4f6f7c9c5b48ad72e979775b7e81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PaZXkNul--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/NYqAsCi.png)

# 什么？

所以这可能是一个惊喜，事实上，当我几个月前第一次做这个实验时，我并没有预料到这一点。

这里发生的事情是，Azure 架构师决定让人们可以轻松地将 web 应用程序(应用服务)从一个地区迁移到另一个地区，而无需更改用于计算时间和时差的代码。所以他们在 UTC 上运行所有的 Azure 服务器。另一方面，这样做的缺点是，当您从内部迁移到云时，您可能需要修改代码来考虑这一点

例如，如果我在瑞士，并将我的应用程序部署到西欧(位于荷兰)，我希望我的应用程序在 Azure 中的`DateTime.Now`与在本地的完全相同。可是...

# 鼓舞士气的故事...

这里的一大教训是，如果你想迁移到云，时区突然变得非常重要，但不一定是以你想象的方式。并不是说你需要**知道你的代码将在哪里运行**。那就是你需要**知道这些区域问题会被抽象化**。

这将是一个很好的主意，通过做正确的事情来为迁移做准备:不要在代码中使用`DateTime.Now`,而是使用`DateTime.UtcNow`,并在需要的地方进行转换。通过这种方式，您已经抽象出了代码的位置，当您迁移到云中并且位置变得不相关或不可预测时，您的代码将继续工作而不受影响。

快乐编码

【人名】劳伦