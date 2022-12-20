# Hypebot 是炒作的人，从来没有让我失望

> 原文：<https://dev.to/transposit/hypebot-is-the-hype-man-who-never-lets-me-down-5bfm>

莎拉·布朗写的

[![](img/93c571d7874c14e99186e938b1aa6deb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZHPLmnPJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/479/0%2AqtlNrmUh3TaXnaiH)

### 炒作的诞生

现在是我们公司 hackweek 的中期，而我的 hackweek 项目却无法运行。我正试图将[矮人要塞发布说明](http://www.bay12games.com/dwarves/)转换成表情符号，但是我不断达到速度限制——可能更有害的是——没有矮人的表情符号🤷。在一项关于我们为办公室买什么口味的华夫饼的民意调查中，我的人数远远超过了其他人。

<figure>[![](img/86ffd66d27bd4e785c69ffb17bcb2701.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FoXHKYrF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/623/0%2A2UDoLroq0OWeuhvf) 

<figcaption>咸焦糖阴谋</figcaption>

</figure>

我知道我必须做什么:放弃我的 hackweek 项目，写一个投票给苹果肉桂的 slackbot。不幸的是，Slack API 不允许机器人点击按钮😒。我改变了策略，创造了 **hypebot** ，一个对我在 slack 中所说的一切做出强烈反应的机器人。

[![](img/9e2ae167bcd306c2bcbccdc7304f325a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CGc6Mjqj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AWqXjF0JYTNQQtNSu_iZrhg.png)

段誉也是，因为他渴望炒作。

[![](img/9a55bb6fac63875ce5a5da61e9a22376.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SzcDg6rt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AqsL9A6GWv93Hhyd2nLP2tw.png)

### 炒作-周期

[![](img/cb087e5ec1683033fe598c99dce2d20b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D1LIMAxw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/722/0%2ADXaA3Nqd3t2rDkWf)

Hypebot 使用 Slack 的[事件订阅 api](https://api.slack.com/events-api) 构建，后端运行在[转置](https://www.transposit.com/)上。你可以在这里查看 hypebot 的所有代码:【https://console.transposit.com/t/transposit-sample/hypebot T4】

<figure>[![](img/81e99179e67f0f76785ba965f4de2438.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NHKXKwVo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A3Y-xtwikGP0163Qy) 

<figcaption>换位控制台</figcaption>

</figure>

当 hypebot 从 Slack 接收到一个消息事件时，它将字符串拆分成离散的单词，并在同义词库中查找每个单词。然后，在数百个同义词的武装下，它将每个单词与所有潜在的 Slack 表情符号进行比较。当它找到匹配时，它会做出反应。这样，炒作周期就完成了。

[![](img/d545e1e178f9c1306a613a44205e264f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8evENYNz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/774/0%2AoO1UdIAatWfbO-N9)

在 Transposit 中实现这一点意味着我不需要部署自己的服务器，Transposit 使得在进行 API 调用时跟踪 bot OAuth 范围变得容易。没有基础设施；没有 auth 纯炒作。

### 更上一层楼

炒作列车并没有在这里结束。我有即将到来的 hypebot 帖子，关于我们如何解决当事情过热时的性能问题，以及我们如何修改 hypebot，以便他最终在 Slack 应用程序目录上首次亮相。当然，如果你太兴奋而不能等待，那么继续 [fork hypebot on Transposit](https://console.transposit.com/t/transposit-sample/hypebot?readme=true) 来构建你自己的 slackbot。

[![](img/5d934987f17dae23bf3c53444fbbc452.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2IF0Ftjt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AlEnHnzacA4aRbi_s)

遗憾的是，hypebot 不能通过 api 给一个中等大小的帖子添加掌声(相信我，我已经检查过了)，所以你只能用传统的方法:🙏👏

* * *