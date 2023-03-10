# 网络市场和平台的胡萝卜:如何设计非金钱激励

> 原文：<https://dev.to/tedcarstensen/carrots-for-web-marketplaces-and-platforms-how-to-design-non-monetary-incentives-e53>

本文最初由康尼·关撰写。

如果你有一个平台或双边市场，如 App store、优步或 Etsy，你对这种情况很熟悉。市场的一面是库存面，即提供应用程序、游乐设施或工艺品的人。另一边是买方，人们购买应用程序、游乐设施或工艺品。

有时候很简单，库存和买方都想要货币交换。但有时并非如此，买方愿意支付，但库存方并非出于金钱动机。后者是开发人员社区面临的一个常见挑战。

### 虚构的例子:Secure.ly

让我们考虑一个安全平台公司的例子。让我们称这个虚构的公司为 Secure.ly。这个平台将依靠专家志愿者来报告漏洞，并将报告出售给买家。全球志愿者社区报告了世界上许多数字安全漏洞。他们是好邻居，看到什么就会说什么。他们是专家，他们是匿名的。CISOs 和安全主管关注该社区的聊天内容，以了解最新的数字漏洞和不良行为者。

CISOs 是 Secure.ly 的买家，好邻居提供的信息就是库存。Secure.ly 面临的挑战是激励这群好邻居加入这个平台。但是就像任何一个好邻居一样，金钱从来都不是目的。**该怎么办？**

[![Carrots For Web Marketplaces And Platforms](img/778c3b1afe0dfb0fc62769b3d237c655.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jFHor2Mh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.heavybit.com/wp-content/uploads/2018/11/carrots-for-web-marketplaces-and-platforms-question.jpg)

> 你最终想要的是*用你能得到的胡萝卜来激励你想要的行为。*

你可能会发现你需要创造一些胡萝卜。虽然钱是一个可能的胡萝卜，但如果你花时间听取你的库存供应商的意见，往往会有更有效、更便宜的胡萝卜提供给你。

谷歌擅长利用“便利性”和“功能性”这两个胡萝卜诱惑用户提供各种私人信息。这些数据是谷歌的库存，然后汇总出售给广告商。脸书使用“你网络上的朋友”的胡萝卜来诱使私人信息进入他们的库存。对于 Secure.ly，我们必须首先了解他们的目标角色最感兴趣的是什么。

### 1。库存提供商想要什么？

[![Carrots For Web Marketplaces And Platforms - Outreach](img/7704e83f955396a6241832324b1edce7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XlYIuw9z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.heavybit.com/wp-content/uploads/2018/11/carrots-for-web-marketplaces-and-platforms-outreach.jpg)

准确的引用是这里的关键。准确的报价是库存方思维和优先事项的根源。选词往往很能说明问题。以下是 Secure.ly 的好邻居可能提供的一些示例报价:

*   “我想分享知识”
*   “[这些人就像]我的兄弟”
*   “我们互相学习”
*   “我想知道别人从我这里学到了什么”
*   “[我想]有所帮助”
*   "我想被人知道，但只能通过我的虚拟形象."

从这些经过筛选的报价中，可以看出以下是 Secure.ly 的好邻居的优先事项:

*   社区——非常重要
*   名声——有点重要
*   匿名—重要
*   $ —不重要，事实上，甚至可以考虑付费参与

既然已经理解了优先级，是时候找出与这些优先级相关的胡萝卜了。

### 2。我的胡萝卜

让我们来谈谈前两个重点，**社区**和**的恶名**。许多可能的“特征”浮现在脑海中，以满足这些优先级。像分数、星星、条纹和排行榜这样的标准游戏化举措可能会奏效。由于社区希望保持匿名，我们可以放弃面对面的活动。但是聊天和论坛可能有用。

[![Carrots For Web Marketplaces And Platforms - Skeeball](img/0b334f4619ad7b8b31ee5ffb83673540.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mUI7l4ri--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.heavybit.com/wp-content/uploads/2018/11/carrots-for-web-marketplaces-and-platforms-skeeball.jpg)

滑雪球，我最喜欢的游戏之一。通过将球降落到篮筐的斜坡上来增加分数。

一个可以挖掘胡萝卜的成熟领域是你已经提供给买方的特性。使用 Secure.ly，买方将购买一个安全漏洞列表，并且这个列表的**可视化**很可能是买方的一个特性。这种情况如何转变，成为库存方面的一个强有力的特征？

在 Secure.ly 的例子中，它认识到他们的好邻居也希望看到他们的贡献。因此，通过升级可视化功能来显示哪个好邻居导致了每个安全漏洞，该功能现在可以成为库存方面的胡萝卜。

一旦你挖掘并列出了所有可能的胡萝卜，并了解了每种的优势，是时候弄清楚你想要什么样的平台了。

### 3。你作为平台想要什么？

吸引买方和库存方…咄！是的，但是什么样的库存方面？有好的和更好的吗？你怎么知道？这是一个重要的问题，因为你的库存决定了你的买方收到的最终产品的质量。你的库存质量越高，你的产品对你的买方就越有吸引力。

再以谷歌为例。他们有一个名为谷歌意见奖励的产品，提示参与用户回答特定的调查问题。它通常用于收集特定商店的访问粒度数据。它会问“你最近去过这些商店吗？选项 A、B、C、D”然后接着问“您在这家商店是如何付款的？”通过收集这种粒度，他们可以准确地告诉他们的买方，他们的广告商，关于支付选择以及其他用户信息。强大。

让我们回到 Secure.ly。在这种情况下,“最好的邻居”将是一个专业的志愿者，他经常发布独特的内容，并对平台保持忠诚。所以我们有…

*   发布频率—高
*   独特内容—高
*   平台的独占性

现在是时候把这些点连接起来了。

### 4。分发胡萝卜

[![Carrots For Web Marketplaces And Platforms - Rewards](img/e6c5c19e2c3e96a6956581030b2ecfcf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--auyxgdIE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.heavybit.com/wp-content/uploads/2018/11/carrots-for-web-marketplaces-and-platforms-rewards.jpg)

记住最终的目标是用你能得到的胡萝卜来激励你想要的行为，然后

> 你如何分发胡萝卜是你激励设计的核心。

这没有处方，每个平台都会通过实验找到自己的方法。但是有一些好的起点。对于 Secure.ly，让我们说他们最强大的功能是**可视化**功能。该功能可以分为一个功能较弱的版本和一个功能较强的版本。从这里，我们可以向最好的邻居提供最强大的可视化。

这一战略将我们最强大的胡萝卜与我们最重要的库存供应商结合起来。

就像你会为你最重要的买方客户提供 VIP 服务一样，这也需要在库存方面同样适用。事实上，可能有让库存方为可视化的高级版本付费的实验空间，以便他们可以实现与社区中的其他人联系的目标。有趣的是，相同的功能可能会针对市场的不同方面进行不同的包装，这对您的平台来说是一个非常强大的机会。

所以你有它…

> 非金钱激励设计=用你可以得到的胡萝卜来激励你想要的行为。

我很想听听你自己的激励设计的例子…请在 [Twitter](https://twitter.com/conniemkwan) 上告诉我！不断创新。

在开发者工具领域，你有什么有价值的见解和经验想与我们的社区分享吗？[我们希望听到您的意见，请立即加入我们的贡献者计划。](https://www.heavybit.com/library/contributor-program/)T3】