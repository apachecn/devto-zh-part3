# 游戏痴迷+网页开发抱负=？

> 原文：<https://dev.to/tangweejieleslie/gaming-obsession-web-dev-aspirations-1dkp>

由[格伦·卡斯滕斯-彼得斯](https://unsplash.com/photos/0woyPEJQ7jc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)、[克莱门特·H](https://unsplash.com/photos/95YRwf6CNw8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)和[马库斯·斯皮斯克](https://unsplash.com/photos/hvSr_CVecVI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)的照片合成的图像

## 一周的进度

上周，我发布了一个[帖子，详细介绍了我制作第一个真正的网络应用的计划](https://dev.to/tangweejieleslie/a-beginner-s-plan-in-launching-the-first-web-application-im7)。回顾过去，我很高兴我选择了一个游戏实用程序作为我的第一个项目。

### 为什么？

第一次，我在这个旅程中取得了进步，不仅仅是一天，而是整整一周。没有“精疲力竭”的迹象，没有通常的不安或分心的迹象，只有持续进步的迹象！

*这只是我的假设——我认为通过从事一个与我痴迷的东西直接相关的项目，我在玩游戏时自然进入的**心流状态**会溢出到我的网络开发中。*

这可能是因为我的应用程序将直接帮助我在游戏中取得进展。可能是因为应用程序与游戏如此密切相关，我已经开始将工作视为游戏的一部分。

不管怎样，事实是我从来没有经历过能够连续工作这么长时间，连续这么多天的情况。除非我认为玩游戏是工作。

## 显示开发

暂定名:SWU

该应用程序接收关于用户游戏资源的数据，如怪物和符文(这款游戏的装备等价物)，并让用户知道如何改善游戏不同部分的阵容。

### 实现了功能:“世界 Boss 分析”

#### 游戏背景

*   “世界老板”是玩家挑战获得战利品的日常内容
*   分数越高，获得的战利品越多
*   更高的分数导致更高的排名，这增加了收到的额外货币的数量

#### 特性的价值主张

*   分析用户的世界老板团队，并提出建议，以提高他们的分数

#### 我的世界 Boss 团队案例分析

1.  用户从上传数据开始。
    [![](img/799f3d61d88448d6711a7ab66f4d68e8.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--sFcENa4X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/caulm5g28hnw4pztn1me.JPG)

2.  影响世界 Boss 评分的因素之一是“属性对决”。第一个分析告知用户他们当前阵容的属性细分。
    [![](img/0c5dcb85db79b21e8af4fc57b7f15ad3.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--qAF6-Lrq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kulhmfredtrk0acxn7jb.JPG)

3.  下一个影响分数的因素是符文质量。这个部分给用户一个他们阵容的符文质量的概述。
    [![](img/b1a64c2f308a089c13c2036225bd0605.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xgliyLyb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/35poyzvcioa8axxorhbp.JPG) 
    对我来说，我已经意识到我的一些单位丢失了符文，而且有些符文甚至没有升级！去显示手动跟踪 1000 个符文和 100 个怪物是多么困难。

4.  最后，还有一个没有完全熟练的怪物列表。
    [![](img/2997e53f8c0e90ffe9f793546d8e40dc.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--HlRSlYhB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ao515ead8m5ghj1wyg0g.JPG)

## 接下来

*   了解如何更改通过 HTTPS 提供的应用程序
*   利用当前组件创建其他特征
*   使用 Vuetify 改进 UX/用户界面
*   实施网站分析，以确定哪些是最常用的功能

## 实施挑战

我最初在获取游戏资源时遇到了麻烦，在[亨利](https://dev.to/badrecordlength) ( [@badrecordlength](https://dev.to/badrecordlength) )的建议下，问题得到了解决，即搜索其他类似应用程序提供的现有 API。

使用 API 从其他服务器获取数据会导致另一个问题，加载时间长。在我对一个巨大的数据集进行初步测试时，大约有 400 多个独特的怪物，加载时间非常糟糕。我最终编写了一个 python 脚本，从现有的 API 中获取所有数据，并通过 JSON 文件在我的网站上提供服务。不确定这是不是一个好的做法，但是速度变化是白天和晚上的区别。

## 结论

首先，在这个项目中工作非常有趣。第二，我从来没有想象过，我有可能在短短一周内推出一个功能应用程序。最后，我感觉那种难以捉摸的心流状态，以前只有在我玩游戏时才能达到，现在变得触手可及，即使只有在工作中我才能直接联想到游戏。

[如果你想亲眼看看“](http://swu.leslietang.com/)[暂定名:SWU](http://swu.leslietang.com/) ”，请点击这里！