# 如何使用 AWS lambda 在云中每分钟免费运行一个 python 脚本

> 原文：<https://dev.to/gp2mv3/how-to-run-a-python-script-in-the-cloud-every-minute-for-free-with-aws-lambda-1k2j>

对于我几个月前做的一个兼职项目，我需要每天每分钟(大约)运行一个小的 python 脚本。我终于找到时间来写实现了。

### 项目

我建立的项目，[NotNow.co](https://notnow.co)，是一个根据使用的电子邮件地址，在特定时间回复收到的电子邮件的服务。

例如，通过向[in2days@notnow.co](mailto:in2days@notnow.co)发送(或转发)一封电子邮件，该服务将在 2 天后将你的电子邮件发回。作为一名企业家，我需要这个工具来提醒我重要的截止日期、账单、销售咨询等。我想要一个轻量级的解决方案，不想为 VPS 或其他东西付费。

我从来没有玩过 Lambdas 和无服务器，所以我尝试了一下，并开发了脚本。维基百科对无服务器概念的定义如下:

> 无服务器计算是一种云计算执行模型，其中云提供商运行服务器，并动态管理机器资源的分配。

我的项目似乎是 Lambdas 的一个典型案例，因为我不想管理它周围的任何事情。这个脚本需要不到一两秒的时间来执行，并且不需要很多资源，只需要一个互联网连接。

### AWS 自由层

AWS 有一个免费层，包括每月 100 万次 lambda 执行，是的，这是很多！

EC2 还包括每月 1GB 的数据传输到外部世界(AWS 服务之外),因此可以永远免费运行我的脚本！

一个月有不到 60 个⨯ 24 个⨯ 31 = 44640 分钟，因此有 44640 次λ处决。

除此之外，免费层包括更多的 AWS 服务。

<figure>[![](img/30c1776445ba84a63fbf12f7393562b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nION8ayI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-TWCjvht6OKiKQeKtuw-0A.png) 

<figcaption>免费等级有 3 种不同的优惠</figcaption>

</figure>

### 现在，如何每分钟触发一次？

选择平台(AWS Lambda)后，问题是:如何每分钟触发功能？

Lambda 函数的执行可以由不同的方法触发，但是要在特定的时间执行您的函数，您需要“CloudWatch Events”触发器。执行规则的定义类似于 cron 规则。您也可以选择一天中的特定时间或特定时间。

<figure>[![](img/3982b6b8b43064f47227277c56444755.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GGv2lt_0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGspPNzd_geqTUzk3iH_bYg.png) 

<figcaption>我的 CloudWatch 事件的每分钟规则示例</figcaption>

</figure>

对于我的项目，我想保持简单，所以我简单地检查每一分钟。一个好的优化方法是通过第二个 lambda 调度发送，以减少执行时间。

### 最终设计

将脚本复制/粘贴到 AWS 后，设置环境变量和触发器，Lambda 定义如下:

<figure>[![](img/0bcd967af46fa0947b53ccafdcee5855.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6KSu4fnO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AylcElXwybT4ZLJP3HkiE9g.png) 

<figcaption>我的提醒λ设计</figcaption>

</figure>

每次执行都会被记录下来，如果函数花费的时间太长，或者脚本失败，就会发出警报。这确保了该功能始终运行，并且我不会错过重要的提醒。

希望这个故事可以帮助理解如何免费每分钟执行一个 python 函数。不要犹豫，留下评论吧！