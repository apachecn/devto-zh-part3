# 测试新功能之前应该问的问题

> 原文：<https://dev.to/dowenb/questions-you-should-be-asking-before-you-test-a-new-feature-ea9>

如果你的新测试分析，你可能会发现它令人生畏。你从哪里开始？想知道一个测试分析师的根本超能力？提问！

给你，给我一些赠品！在你开始测试下一个项目/特性/用户故事或 epic 之前要问的问题。

# 提问

## 1 -谁能做到这一点？

[![Who](img/f1b5a09027dcc0ea8ab43c34ef4c6d85.png "Who")](https://res.cloudinary.com/practicaldev/image/fetch/s--3Em5YkTm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ulpzmsihbykj7mwmj0op.jpeg)

经常被基本的“作为一个我想要的用户”风格的用户故事所忽略。确保您真正理解系统中允许哪些用户和角色使用该功能。

跟进一些问题，比如“当一个用户被阻止做某件事时会发生什么？”以及“这在 API 和 UI 上都被阻止了吗？”最后是“我们会遇到什么样的错误？对用户来说是可操作的吗？”

也许该功能可以匿名使用？

## 2 -他们什么时候能做到？

[![Time](img/002106175ff525a333bd87dada804e1c.png "When")](https://res.cloudinary.com/practicaldev/image/fetch/s--Z9uao4B4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ycosm5rbiw5nrgmkkv8.jpeg)

允许用户使用此功能时有什么限制吗？他们是否需要某一级别的有效订阅？他们可以随心所欲地做很多次，还是只能做有限几次？

可以阻止用户这样做吗？也许该功能可以按用户或公司启用/禁用？A/B 测试和金丝雀呢？

## 3 -乘数有哪些？

[![Platforms](img/da77e472d4d93ce8e6bb70c5ddabd791.png "Platforms")](https://res.cloudinary.com/practicaldev/image/fetch/s--S1fz6oCZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ssjvvbjyytzt8dt89ges.jpg)

对于一个只有 iOS 的应用程序，你的平台乘数是什么可能是显而易见的。用 x 代 iPhone 和 iPad 测试一下，你就可以开始了。

但这在其他情况下并不总是那么明显。是针对移动/桌面/VR 吗？您是否在不同的设备上支持不同的浏览器？

或者，可能有一些特殊的配置或模式，或者与另一个特性的交互，会使您需要运行多个测试？

确定什么样的环境选择意味着代码将遵循不同的路径——这可能会导致问题，因为您的代码被不同地解释(IE11 就是一个典型的例子)。

## 4 -我怎么知道它正在做预期的事情？

[![Logs](img/386209462b352043e29f78dc0b05b32b.png "Logs")](https://res.cloudinary.com/practicaldev/image/fetch/s--z-Qq3ZjF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4fbu42k84adkk13xbuw0.jpeg)

可观测性和可测试性是热门话题，这里不赘述。但是一定要记住弄清楚如何让你自己满意这个特性做了你期望它做的事情。

这会改变数据库吗？如果会，你能访问数据库吗？

这会被记录到任何地方吗？如果失败了会怎样——我们能证明它没有发生吗？记住，日志或它没有发生...

## 5 -这如何与现有的或计划的功能交互？

[![Plan](img/c9ba8462ffb9d76f46a9c3ddaee13930.png "Plan")](https://res.cloudinary.com/practicaldev/image/fetch/s--52ppVqiK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pecz13tl5n7dutrhzm9g.jpeg)

新事物如何与旧事物一起工作？新功能是否会扩展现有的日志记录、审计和身份验证系统？因为我们增加了新的数据库调用，性能会有下降的风险吗？

最后一点是关于识别集成风险，所以回顾现有系统的文档会有所帮助。

# 有更多问题吗？

[![Questions](img/713d55e6c158ee6d0233f1e3ba9116cb.png "Questions")](https://res.cloudinary.com/practicaldev/image/fetch/s--PBF5p7PH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ytkmtwmjgxngx8y7bcs0.jpeg)

很好！问他们——如果你对新特性有任何疑问，请随时在评论中告诉我。我渴望增加我的股票问题列表，所以我期待着你的来信。