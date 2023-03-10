# 利用 IFTTT 克服 Crowdfire 的 Twitter 困境

> 原文：<https://dev.to/walkingriver/using-ifttt-to-overcome-crowdfires-twitter-woes-okn>

从[walkingriver.com](https://walkingriver.com/using-ifttt-to-manage-crowdfire/)交叉发布

最近，社交媒体工具 Crowdfire 的 Twitter API 在没有任何警告的情况下被暂停。突然间，它在世界各地的用户向 Twitter 发布策划内容的能力完全关闭，让他们没有好的方法来自动化他们的社交媒体参与。在这篇文章中，我将解释我如何使用 IFTTT 来克服他们的暂停，让 Crowdfire 做它的事情，并让我继续在 Twitter 上发布文章。

## 背景

我使用 [Crowdfire](https://web.crowdfireapp.com) 来帮助我的大部分社交媒体。如果你不熟悉 Crowdfire，它自称是一个从单个仪表板管理多个社交媒体平台的工具。基本上，我用它来帮助我在网上查找、整理和分享与我作为软件开发人员的日常工作和我的几千名 Twitter 粉丝相关的文章。

Crowdfire 的工作原理是在网上搜索与你提供的关键词相匹配的文章。它提供了一个每日文章列表，其算法与这些关键词相匹配。它不会自动发布。您可以选择要发布的文章和要忽略的文章。如果你发现某些网站上的文章完全不相关，你可以屏蔽它们。

一旦你选择了一篇文章，Crowdfire 就会自动将它发布到你的一个或多个社交媒体账户上，要么立即发布，要么按照 Crowdfires 标注的“最佳时间”发布。这意味着 Crowdfire 将根据你告诉它每天发布多少帖子来决定何时发布。你的每个社交媒体账户可以有不同的时间表，Crowdfire 会为你管理。

我成功地使用 Crowdfire 建立了一个由志同道合的软件开发人员组成的适度的 Twitter 追随者。我每天只花几分钟的时间来回顾十几篇文章，并分享我觉得我的追随者和我会觉得有趣和有用的文章。这是一个伟大的工具，直到它不是。

## 推特暂停

大约在 1 月底，Twitter 在没有警告或通知的情况下暂停了 Crowdfire 的 API 访问。尽管有无数的上诉，Crowdfire 已经不能在 Twitter 上发布内容了。2 月 7 日，Crowdfire 发布了一个更新，描述了发生的事情以及他们正在做的事情。目前，一切都没有改变，Crowdfire 仍然无法发布到 Twitter。它仍然可以在其他社交媒体网站上发帖，如脸书和 LinkedIn，但对我来说，没有什么能比 Twitter 的潜在覆盖面和影响力更大。

作为一名软件开发人员，我每天都在解决问题。这个问题肯定有解决的办法。这时我想起了 [IFTTT](https://ifttt.com/) 。

## IFTTT 来救援

Web 开发人员倾向于从构建模块的角度考虑问题。可编程网络是这些积木的巨大集合，假设你知道去哪里找。IFTTT 是一个有点笨拙的首字母缩写词，意思是 IF This Then That。它由一组食谱组成，每一个食谱都采取“如果这件事发生在你的一个网络账户上，那就做另一件事”的形式。IFTTT 仍然有能力发布到 Twitter 上，所以我心想，“我想知道 IFTTT 是否可以观看我在脸书的新帖子，然后自动交叉发布到 Twitter 上？”事实证明，可以，而且真的很简单。

## 注册并连接

我要做的第一件事是向 IFTTT 注册，并连接我的社交媒体账户。因为我打算把它连接到脸书，所以我决定用我的脸书账户注册。

[![Registration](img/fa7603f65664febe9a44c83ed17e69f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S7qkL7vK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walkingriver.com/assets/img/ifttt-02.png)

接下来，我需要连接我的 Twitter 账户，它是 [@WalkingRiver](https://twitter.com/walkingriver) 。

## 新脸书页面

之后，我需要一个地方来张贴我的内容。我不想用这些东西给我的家人和朋友增加负担，所以我创建了一个新的公共商业页面，也叫 WalkingRiver。如果你有兴趣看的话，它就在这里。

## 添加一个小程序

然后我试验了一些提供的各种食谱，IFTTT 称之为小程序。他们中的一些人比其他人工作得更好。我试的前几个根本不行，有一个不一致。其中一个太好用了，我就关了。下图显示了我尝试过的那些(禁用的，灰色的)，以及最终我决定使用的那个(蓝色的)，它看起来工作得很完美。

[![My Applets](img/77c9c4627f05a9f62fbc967c2195003d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UkKxsbhw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walkingriver.com/assets/img/ifttt-03.png)

我按照菜谱的配置，简单地将它指向我的新脸书页面，让它自动交叉发布每一篇 Twitter 帖子。

[![Facebook Post](img/21b31d79f925c82f82d53928d6d6bddc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2r7W4m5c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walkingriver.com/assets/img/ifttt-04.png)

结果如下所示。

## 结果

这是我在 Crowdfire 上选择的一篇文章，它出现在我的脸书页面上。

[![Facebook Post](img/8371aefa90af3bf653710b8c3dea5316.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i6ONk8iM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walkingriver.com/assets/img/ifttt-05.png)

几分钟后，IFTTT 在推特上发布了这个

[![Facebook Post](img/3225d9a1db4f49b113fb8d7e1a5e53e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7UUqof0g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walkingriver.com/assets/img/ifttt-06.png)

## 结论

使用 IFTTT 解决了我在使用 Crowdfire 向我的社交媒体追随者发布内容时遇到的直接问题。

在这一点上，我唯一的抱怨是发布到 Twitter 上的链接是 IFTTT 的一个缩短链接。目前，我对我所拥有的很满意，特别是考虑到相关的配置。做对并不太难，现在我可以花时间审查内容，而不是发布内容。