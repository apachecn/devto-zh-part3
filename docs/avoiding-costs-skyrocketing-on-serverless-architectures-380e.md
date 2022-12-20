# 避免无服务器架构成本飙升

> 原文：<https://dev.to/byrro/avoiding-costs-skyrocketing-on-serverless-architectures-380e>

[![Rocket photo by NASA](img/56bd05c7237d031b530c075d347ada68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9IFQ_8Qn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/umtagj82vos9r8wutvw9.jpg) 

<figcaption>照片由 [NASA](https://unsplash.com/photos/n463SoeSiVY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上传 [Unsplash](https://unsplash.com/search/photos/rocket?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

AWS Lambda 等 FaaS 服务的可扩展性和可靠性确实给了我们一个无价的想法。尽管如此，如此大的灵活性需要仔细监控，以确保我们在性能和财务成本的安全范围内运行。

假设部署了一个功能来满足某个面向客户的请求。对该功能的基准测试显示，完成一个请求平均需要 3 秒钟——标准偏差非常低。我们计划加价 200%,并向我们的客户收取运行这 3 秒钟的三倍费用。

现在想象一下，这个函数开始花 10 或 20 秒来处理请求？也许我们依赖于第三方 API，他们的网络性能开始变得非常糟糕。见鬼，它可能持续一天或一周，我们无法控制。根据我们的应用规模，这种不匹配可能会导致巨大的运营损失。

## 不要烧掉你宝贵的美元！

[![Burning US dollar bill](img/c6a3334d9cffb1689b92c3893c7da8ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z8nfaySr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pprq3oa8cbkwv30pyxrp.jpg) 

<figcaption>照片由[克里斯](https://pixabay.com/users/intellectual-4717896/)上传 [Pixabay](https://pixabay.com/photos/burning-money-dollars-cash-flame-2113914/)</figcaption>

为了避免这种问题，我们需要制定周密的逻辑来监控我们的功能指标，并在任何事情开始偏离轨道时提醒自己。在上面的例子中，我们可以有这样的逻辑:

### **提醒** me 如果调用**持续时间**超过 **3.5 秒**，在**平均**，超过最后 **15 分钟**。

警报可以是电子邮件、发送给空闲频道的消息、SMS 等等。重要的是能够以符合我们应用程序性能预期的方式设置自定义监控策略。

这不仅仅是监视错误或单独分析每个调用。由于硬件、网络等的可变性质，我们的功能可能会以不可预测的方式执行。在调用的基础上测量它会导致许多错误的警报。许多请求可能超过 3.5 秒，但许多其他请求低于 2.5 秒，这将抵消我们的成本，不应该占用我们的宝贵时间。上述警报政策中的“最后 15 分钟”汇总部分对于我们节省时间和关注真正重要的事情至关重要。

这也是 Dashbird 存在的原因之一。这是一个专门为无服务器应用程序设计的监控工具，为设置自定义监控策略提供了极大的灵活性，例如上面演示的策略。如果你有在 AWS Lambda 上运行的功能，我强烈建议你去看看，你可以免费试用，不需要信用卡: [Dashbird.io](https://dashbird.io) 。

* * *

雷纳托·拜罗是 Dashbird.io 的开发者拥护者。你可以在 [Twitter](https://twitter.com/@byrrorenato) 和 [Medium](https://medium.com/@byrro) 上关注我。