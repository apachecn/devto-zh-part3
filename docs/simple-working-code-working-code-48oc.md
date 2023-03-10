# 简单工作代码>工作代码

> 原文：<https://dev.to/aritdeveloper/simple-working-code-working-code-48oc>

最近，我的任务是创建一个重定向，从我公司的 Rails 应用程序重定向到一个具有相当长的 URL 的外部网页。

首先，我在页面的控制器中创建了一个动作:

[![Controller](img/02d3e37ab6b8b31dabb827a50f48bf53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F6K31-da--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n8jfjl5nwo8y9hz7fxau.png)

接下来，我为所述动作添加了一条路线:

[![Route](img/3d4458b33e7ae5ac27ddfe3384f883c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0OiznBj1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9s08bojslp4x8l8edl24.png)

然后我在页面中调用路线:

[![View](img/246f10828c2fd59874db83c4a0c09b75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kZxLbw6G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o9uaywn1dzdtayp3qowc.png)

在审查代码时，我的队友建议我创建一个返回 URL 的*助手方法，然后在视图中调用助手:*

[![Helper](img/3f083b99178c9ca752b1fcce12038d0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0UURFe8F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ucmm1pty5sxphorqke66.png)

[![New View](img/06af059a16891232c47c363b57b400d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i1iDY89n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s616vpv37pkjpybq06mu.png)

* * *

管制员的行动是不必要的，多余的，麻烦的。实现 helper 方法是如此简单、整洁和直接。
一些代码可以工作，但这并不能免除对这些代码的改进。