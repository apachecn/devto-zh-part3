# 鸡蛋比煎蛋卷更容易运输

> 原文：<https://dev.to/korenmiklos/eggs-are-easier-to-ship-than-omelettes-1g3g>

> *   I estimated the regression model we discussed last week, but it didn't work.
> *   Which regression model? What do you mean useless?

在你的研究团队中，你多久进行一次这样的对话？我们倾向于假设我们同事的思维与我们的思维神奇地联系在一起。他们不是。事实上，在我的想法和你的想法之间有一条很难逾越的界限。跨越这一界限总是需要真正的努力，这影响了我们的合作方式。

[![Photo by [Jakub Kapusnak](https://unsplash.com/@foodiesfeed?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)](img/f9a9ff8ec35f81f48de19eb02d764907.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E6j7eYHm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AZ3lxEHR8vumzwAfV) 
照片由[雅各布·卡普斯纳克](https://unsplash.com/@foodiesfeed?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我最近在与合著者分享我的作品时引入了一个简单的模板。我回答以下四个问题，并要求他们也这样做。

1.  我完成了哪些可交付成果？
2.  我学到了什么？
3.  我需要你做什么？
4.  我的下一步是什么？

举个例子，

1.  根据桥梁邻近指标估计邮局数量的泊松回归:见表 2。
2.  桥梁建成后，10 公里范围内的邮局变得更加频繁。这种影响在 20 公里以外就消失了。
3.  查看表 2，告诉我要包括哪些额外的控制。
4.  下载河宽数据，用作桥梁定位工具。

它是由每天的 scrum 会议激发的，但是我已经把它适应了研究项目的探索性质。

在问题 1 的回答中，你要列出**实际的可交付成果**(表 2)，而不仅仅是模糊的概念(回归模型)。您应该为发布设置表格和图表的格式，包括注释和标签。无论如何，您在某个时候都必须这样做，您也可以帮助您的同事理解您具体做了什么来生成图 3。

研究是一个探索的过程，你的见解是一个重要的输入。在问题 2 中，你可以分享一下你学到了什么。最让你惊讶的事是什么？不要只是重复表格或图表中的内容。你不想侮辱你同事的智慧。这是一个锻炼你分析判断的机会。

"*"和"*你怎么看？*“别切了。你需要哪些**具体行动**来继续你的工作？如果你被困在某个地方，让他们知道。如果你对某些部分不确定，需要更多的反馈，让他们知道。*

 *就像在 scrum 中一样，分享你的下一步计划有助于团队达成共识。你是决定**下一步**的最佳人选，因为你是最了解你正在处理的数据和模型的人。(如果没有，请在问题 3 中寻求反馈。所以不要害怕规划你的工作。

我有时只是对问题 4 说:“*下一步:没有。我很乐意在周一下午通过电子邮件或 Skype 回答澄清问题。*“最好让你的队友知道他们能从你身上期待什么，哪怕是“*没什么*”如果你们不共用一间办公室，这一点尤其重要。我收到了太多关于谁做了什么的电子邮件，如果人们不同步，这很容易需要一周或更长时间。

我确实感受到了这种方法的好处。我可以更快地赶上我的合作者的工作。我们需要更少的同步状态会议，如果我们需要，他们会更有效率。

这只是一个例子，说明创建一个具有硬边界的分析产品可以让你更有效率。你还应该编写没有副作用的[的](https://dev.to/korenmiklos/everything-is-a-function-4171)[模块化代码](https://dev.to/korenmiklos/the-tupperware-approach-to-coding-1g74)。并且对你队友的计算环境不做任何假设。但稍后会详细介绍。*