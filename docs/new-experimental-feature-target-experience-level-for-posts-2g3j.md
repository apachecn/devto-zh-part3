# 新的实验特性:帖子的“目标经验等级”

> 原文：<https://dev.to/devteam/new-experimental-feature-target-experience-level-for-posts-2g3j>

我们现在在帖子上有一个属性，它接受 1-10 的评分来决定目标受众对初学者的友好程度或帖子的高级程度。

我们的目标是帮助人们发现有帮助的内容，这取决于他们在这个范围内的位置。初学者等。仍然有用，但这增加了一个因素。

记住这一点，我们你现在可以在你的设置的“杂项”部分宣布你觉得自己处于“经验水平”的哪个位置。这是私人信息，仅用于改善您在网站上的体验。

## T2】👉dev.to/settings/misc

在给帖子贴上合适的经验等级标签方面，我们依靠群众的智慧。拥有 mod 权限的用户现在可以在文章的 URL 中添加`/mod`后对文章进行评级。现在也有一个全球[dev.to/mod](https://dev.to/mod)端点能够批量完成这项工作。随着时间的推移，我们将在这篇文章中添加更多有用的功能。

总而言之，这是一个温和的指标，帮助人们看到更多相关的帖子。它的设计目的不是基于目标体验水平完全孤立内容。这个的接口也是相当低级的。将来，允许人们选择更多的描述性词汇，或者可能按照 1-5 而不是 1-10 来排名，这可能是有意义的。

作为这可能派上用场的一个例子，这里有一个精彩的帖子，它以回答“什么是机器学习？”这个问题开始。这对所有人来说都是一个很好的读物，但是绝对倾向于初学者。

[![apoorvadave](img/69fbd57e3402c2edf2f801991ccded67.png)](/apoorvadave) [## 从机器学习开始-第 1 部分

### apoorva Dave 1919 年 2 月 13 日 4 分钟阅读

#machinelearning #python #deeplearning #beginners](/apoorvadave/beginning-with-machine-learning---part-1-pbl)

否则，这篇文章假设了一些关于这个主题和软件开发的更高级的知识。

[![swyx](img/d12fa7711c643b78888cfefb996e1a26.png)](/swyx) [## 无监督学习:信息论概述

### swyx Feb 23 ' 194 分钟读取

#machinelearning #unsupervisedlearning](/swyx/unsupervised-learning-information-theory-recap-4iem)

更有可能从第一篇文章中受益并喜欢它的人将更有可能遇到它，而将从第二篇文章中受益的人将更有可能看到第二篇文章。

我们将会有更多的指导，但是 mods 可以根据经验等级开始贴标，如果他们愿意的话。这个功能将来也会提供给帖子的作者，但是我们会等到整个功能的界面更加用户友好的时候。