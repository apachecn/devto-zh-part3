# 启动测试策略

> 原文：<https://dev.to/d_broadbridge/kick-start-a-testing-strategy-3ok9>

我在开发人员中看到的一个常见障碍是决定他们正在开发的系统的测试策略。

令人欣慰的是，找到一个好的测试策略需要整个团队的参与，而不仅仅是开发人员，因为每个人都有责任让系统按照预期工作。

### 开球

召集所有与你的技术有利害关系的人开会，目的是发现和理解你的技术操作中包含的风险。

让每个人都在便利贴上列出你的技术特点，越多不同的人展示，你就会发现越多的特点，因为某些特点与不同的领域更相关。

有了功能列表后，在白板上画出下面的图表。

[![](img/dd9af178ede2939b5a72f1a204fadf1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6-ypO56C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pqj0boj3p94h2i6yltcj.png)

让每个人把功能放在他们认为合适的地方。因为我在保险方面有一些经验，我将用它作为例子。

[![](img/ddbb9db31f7c9e996deb60009a72b646.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D58mFLha--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iabpkftrmedg5w69jibm.png)

这里我有四个特点:

*   用户可以提交索赔:用户很少提交索赔，如果在线系统有问题，他们可以打电话给我们。
*   用户可以购买一个政策:需要这个来赚钱，但用户只会做一次，它不是世界末日，如果它发生故障。
*   用户可能被拒绝:每个用户在购买保单前都会与该功能交互，该功能的一次故障给企业带来的成本可能是灾难性的。
*   用户得到正确的价格:用户需要得到合理定价的报价，但是这个特性是为了得到一个准确的价格，这个价格没有下跌那么大的风险。

一旦每个人都理解了这些特征，就从左上角到右下角画两条或更多的线。这些定义了如果某个特性出现问题，会给业务带来多大的风险。

[![](img/1140fe25bec29b24ab3b3262bffc2397.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yZvRia6s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4k4h7ixo1nsfi2kk2sid.png)

一旦每个人都同意功能在区域中的位置，结束会议。

### 开发和沟通技术实现

召集所有技术人员，根据风险(基于风险的测试)制定测试系统不同部分的粗略指南。根据上一个示例，您可能会得到类似这样的结果:

[![](img/7878cd8f6483c10b83ddf707bb937554.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NOaNGcWT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/shxfs3uxrck7gipubv57.png)

您可以随意添加琐碎的和关键的行，以便更好地对您业务中的各种风险进行分类。

在这个例子中，我们对拒绝的成功进行了额外的监控，让我们知道服务正在按预期运行。我们还在生产前的每个版本之前手动测试服务是否工作，以 100%确定它在发布之前工作。

一旦技术团队决定了粗略的测试策略，他们就可以向团队的其他成员展示。开发者应该接受任何反馈并适应。

### 学习提高

完美的战略不太可能，重要的是不断改进。

改进测试策略的几个想法是:

*   每当生产中出现一个 bug，评估它的影响，如果它很重要，调整你的测试策略以防止它再次发生。
*   设置度量标准来衡量你的测试策略的结果，我听说过一个有趣的度量标准是逃避缺陷，这是在产品到达客户之前没有被发现的问题。如果您能够准确地度量这样的东西，并通过缺陷的影响来衡量它，它可以提供对您的测试如何执行的洞察。

感谢阅读。

灵感来自:

[https://www.youtube.com/watch?v=RuGFSSUYGXQ](https://www.youtube.com/watch?v=RuGFSSUYGXQ)

[https://www.youtube.com/watch?v=rSY-zqDfc_s](https://www.youtube.com/watch?v=rSY-zqDfc_s)

审核人:@nhnhatquan，@WickyNilliams