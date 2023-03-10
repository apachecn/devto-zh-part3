# 如何为开源做贡献？

> 原文：<https://dev.to/raddevon/how-to-contribute-to-open-source-l87>

## 投稿的力学

为开源做贡献可以通过 4 个步骤来完成。

1.  找到机会
2.  派生存储库
3.  改变你的叉子
4.  提交拉取请求

前三个步骤已经讲得令人厌烦了。如果你对提出拉式请求的技巧感兴趣，Kent C. Dodds 已经创建了一个关于提交你的第一份 PR 的极好资源 [**。**](http://makeapullrequest.com/)

不幸的是，提交拉取请求的机制是最简单的部分。不管你是谁，也不管你参与了什么项目，它们都是一样的。真正的魔法发生在第一步:**寻找机会**。

## 寻找机会的 2 种方法

有抱负的 web 开发人员对开源软件感兴趣，因为他们相信这会让他们在未来的雇主面前更好看。我在 Twitter 上做了一个快速的非正式投票，以查明事实是否如此。这些回答很有趣，也许有点令人惊讶。

> ![Christopher Hawkins profile image](img/9cbb0c1f93e5e82290bfe2896f46003b.png)克里斯托夫·霍金斯@克里斯 _ 霍金斯![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@拉德德文](https://twitter.com/raddevon)作为一名开发者的长期雇主，我对候选人是否为开源做出贡献完全没有兴趣。2018 年 11 月 19 日上午 04:50[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1064380493796323328)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1064380493796323328)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1064380493796323328)

> ![Ben Hyrman profile image](img/36f9cf4dacd75452853cbaff2a02ba75.png)本·海尔曼@ hyr Mn![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@拉德德文](https://twitter.com/raddevon) [@venikunche](https://twitter.com/venikunche) 是的我看。对我来说，他们可以帮助我，也不会伤害我(例如，我不会因为“坏”代码而反对一个低年级学生)。如果这是一个人自己的工作，并且是“实质性的”,那么我就能感觉到他现有的优势和兴趣。2018 年 11 月 19 日下午 14:02[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1064519182463787009)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1064519182463787009)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1064519182463787009)

> ![Stella Pantela profile image](img/ab031213fd805357b8d04ccec9446b2c.png)Stella Pantela@ stylianipantela![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ raddevon](https://twitter.com/raddevon)零分除非候选人在简历上列出一个具体的项目或者提出来。2018 年 11 月 19 日下午 14:23[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1064524496659468288)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1064524496659468288)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1064524496659468288)

> ![Indu Khosla profile image](img/57e7b59a13bcd574670adafa2706ec6d.png)【indu khosla】[@ indukhosa】](https://dev.to/indukhosla)

> ![Brett profile image](img/4461e74b0b50f6545ffad3c9c0820668.png)布雷特@ bzmw _![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ rad Devon](https://twitter.com/raddevon)[@ venikunche](https://twitter.com/venikunche)我不看，除非他们在简历上叫出来。而且即使这样也有那么多事情要考虑:他们只是更新文档吗，他们是否做出了有意义的贡献，项目是否“严肃”2018 年 11 月 19 日下午 19:51[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1064607202470739968)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1064607202470739968)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1064607202470739968)

看起来，至少对大多数回应的人来说，开源贡献不是他们考虑的一个因素。对于一个说他们看了的回答者，我感觉如果你*没有*对开源做出贡献，你不会被取消资格；如果你有，那只是额外的奖励。

即使你在找工作时可能没有注意到，为开源做贡献也是值得的。你可以**帮助开发者社区**、**让自己更加可见**、**解决自己的一些问题**。这里有两种方法可以帮助你找到作为一名新开发人员你可以做出贡献的地方。

### 寻找机会

这是大多数新开发人员采取的方法，因为他们想在面试中开始兜售这种贡献。根据我的经验，**对于新手来说这是更难的方法**。理想情况下，您将选择一个您以前使用过的库，这样您至少对它有些熟悉。然后，您将浏览存储库的开放问题，找到您认为可以解决的问题。

如果你想贡献代码，你需要先熟悉一些代码库，这样你就知道你需要在哪里工作来添加你想要的特性或者修复你所针对的 bug。这对新开发人员来说是一个相当大的要求:抓住一个问题，找出代码库，*和*编写一个 pull 请求来解决这个问题。**阅读代码是*而不是*一项简单的技能**，并且是这个过程的一大部分。

如果你打算走这条路，这里有一些想法和资源可以让你的生活更轻松。

*   如果你想为一个你不熟悉的库做贡献，**从文档而不是代码**开始。这非常适合您，因为无论如何您都将使用文档来学习这个库。你一定会找到弱点。当你找到它们时，为下一个阅读文档的人清理它们。
*   图书馆通常有标签来识别新手友好的问题。比如 React 有个标签叫“[好的第一期](https://github.com/facebook/react/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22)”找出这些来找到你可以解决的问题。
*   许多网站为新手提供了为开源做贡献的好机会。查看[待价而沽](https://up-for-grabs.net/#/)或[令人敬畏的首次公关机会回购](https://github.com/MunGell/awesome-for-beginners)中的几个好例子。由于 awesome list 本身就是 Github 上的一个资源库，你可以通过添加你发现的其他好的新手机会来为它做出贡献。所以 meta！
*   Hacktoberfest 是一年一度的活动，鼓励对开源项目做出贡献。该活动将帮助您找到绝佳的机会，并奖励您一件 t 恤衫！

因为这是最难走的路，而且它似乎对你的工作前景没有太大影响，所以我会避免这种方法，而倾向于一种更“有机”的方法来寻找为开源项目做贡献的机会。

### 等待一个“有机”的机会

当你不再试图强求时，你会发现为一个开源项目做贡献要容易得多……尽管这可能需要更多的时间来寻找机会。

我的第一个合并拉请求是一个名为[望远镜](http://www.telescopeapp.org/)(现在是 [VulcanJS](http://vulcanjs.org/) )的库。这是一个建立在 [MeteorJS](https://www.meteor.com/) 之上的框架，使得建立 Reddit 风格的链接投票社交网络变得容易。我用这个框架为独立游戏开发者建立了一个名为 GameDev News 的新闻分享网站。

Telescope 让我的生活变得简单多了，但是它缺少一个我真正需要的特性:它没有通过 API 公开评论。当你提出请求时，你会得到帖子，但没有评论。

出于几个原因，这是我开始为开源做贡献的大好机会。

1.  我已经熟悉了代码库。我已经用这个框架建立我的网站几个星期了。
2.  这是在解决我自己的问题。这是钻研他人代码并做出贡献的巨大动力。
3.  我不需要去寻找机会。机会来了。

我写了几行代码来添加这个特性，并提交了 pull 请求。维护人员回复了我一个他在回顾时看到的问题。我修复了这个问题，拉请求的 [**被合并为**](https://github.com/VulcanJS/Vulcan/pull/378) ！

我所有的贡献都符合这个模式。有一次，我找到了一个库，听起来它能满足我的需要。唯一的问题是，**它不工作**。它根本没有做到它声称要做的事情！我检查了代码，看是否能找到问题。这真的很简单:这个库在寻找一个返回真值的函数，但是这个函数没有返回任何东西。

我用两行代码修复了它，[提交了一个拉取请求](https://github.com/w-m/pleonasm/pull/6)。那个还是没有合并，(图书馆好像完全废弃了。)但是我能够在我从事的项目中使用我的固定叉子。

如果你继续开发，睁大你的眼睛，**你会发现很多这样的机会**让你解决自己的问题，同时也为开源做贡献。

需要一些帮助来浏览所有这些并增加你成为专业网站开发者的机会吗？安排一次免费的指导会议，我们将一起找出您的下一步行动！

我想帮助你在你的网络开发生涯中迈出**下一步**。加入我的**免费指导会议**，我们将一起帮助你前进！在[拉德德文](https://raddevon.com/)报名。

## 不要着急计时

如果对我的推文的回应是一个指标，你可能不会因为你的贡献而得到一份工作。这意味着时机并不重要。不要强求，让为开源项目做贡献的机会向你走来。这是一个不可思议的目标，但你不必今天就完成它。