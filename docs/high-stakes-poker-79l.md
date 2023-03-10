# 高赌注扑克

> 原文：<https://dev.to/brettshollenberger/high-stakes-poker-79l>

*这篇文章是我的第一原理系列的[测试的一部分。如果你喜欢这个系列，请继续关注我在](brettcassette.com)[egghead . io](https://egghead.io)T5 上的 Cypress 端到端测试课程中的实用测试建议*

如果你总是玩高赌注的扑克，有时你会因为高赌注而被烧伤。

尤其是当你在尝试一些新的东西(比如发布新代码)时，以较低的赌注玩游戏不是很有意义吗？

[![Worst Case Scenario](img/e1608aeb9a661813c013a0695dafb9b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t4owoh0p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://brettcassette.com/static/18ed1ad1e2a54298a3c05504c86aa86f/27660/worst_case-01.webp)

如果你的代码最糟糕的情况是:“没有客户可以整天签出”，那么我们可以使用现代工具，比如 [A/B 测试组](https://github.com/Alephbet/alephbet)来确保你的代码只发布给 1%的客户，极大地降低我们的风险。现在，最坏的情况已经减少到“1%的用户一整天都无法结账”

但是，我们如何能够更早得到警报，使我们的中断不是一整天？有了像 AppOptics 或 Amplitude 这样的仪表板工具，以及像 PagerDuty 或 Pingdom 这样的自动警报，我们可以更快地检测到异常行为，只要我们预先定义我们的指标是什么。

当检测到异常时，我们可以依靠我们选择的特性 flipper 库来为每个人关闭代码路径。也许我们已经将我们的停机时间从“没有人可以全天结账”减少到“1%的用户 1 分钟都不能结账”

更好的情况。

测试只是我们对代码感到自信的方式之一。我们越能降低最大风险，我们的损失就越小。就像投资组合多样化一样。

我认为重要的是，我们从给自己最低的风险开始——在我们开始评估测试的价值之前，从最坏的情况开始。为什么您希望每次部署都有可能让公司损失一大笔钱呢？

在您开始测试之前，请记住权衡风险所在，并考虑您可以使用哪些替代方法来降低风险。当测试对代码信心至关重要时，[确保你的测试没有提供虚假的安全感](http://www.brettcassette.com/false-sense-of-security)

*这篇文章是我的第一原理系列的[测试的一部分。如果你喜欢这个系列，请继续关注我在](brettcassette.com)[egghead . io](https://egghead.io)T5 上的 Cypress 端到端测试课程中的实用测试建议*