# 我的副业想法

> 原文：<https://dev.to/joro550/my-side-project-idea-6dj>

前几天，我在寻找一些构建网站的好组件。实际上，我必须记住语言是重要的，我不应该夸大已经定义好的语言，更喜欢对我试图描述的东西进行更长的解释。

# 我的用例

我喜欢在业余时间写代码，但我讨厌做那些我不应该做的事情，在我看来前端就是一堆将模型映射到表格或图表的东西。我发现 99%的时间我只想把数据从我的服务器映射到我的客户机上的一个表，但是这引发了一系列问题

## 问题:

*   页码
*   搜索
*   整理
*   列

我想要一些具有良好智能的东西，我几乎可以从一个视图复制和粘贴到另一个视图，做一些模型和属性设置，然后就可以了，基本上我想在前端花尽可能少的时间。

好消息是，我们有很多库可以提供帮助，jQuery 数据表、制表器都是不错的选择。作为一名 c#开发人员，有一个(或两个)工具真正引起了我的注意，那就是 devextreme/devexpress 工具(看起来非常类似于 telerik 工具)。他们有一些非常好的 c# razor 包装器，可以很好地相互映射，我几乎立刻就爱上了他们。

直到我看到价格！每个开发者每年 499 美元。

我不再缺钱了，但对于我自己能写的东西来说，这是一大笔钱。

# 写自己？

啊，是的，我们到了！这是我现在的副业，我想它会分成两部分，第一部分是 JavaScript。它将负责获取您的信息并将其显示在屏幕上！会有一个 c#库给你一些剃刀语法。

# 为什么不用现有的库？

我可以，外面有很多，老实说，我已经有一段时间没有玩 JavaScript 了，所以我想象一个相当大的 JavaScript 挑战，看看有什么变化。这样我也能更好地控制我正在做的事情，我会更好地理解它。它可能永远不会像其他的库一样好，但是我只是想要一些可以在我的网站上使用的东西，所以我花时间写了一个实用程序！

# 一些最初的决定

*   Typescript 而不是 JavaScript **我的意思是为什么不，它是一种非常好的语言，给了我编译成任何 JavaScript 版本的选择
*   Chai/Mocha 看起来和其他测试框架一样好，不明白为什么我不应该使用它
*   我很懒，我不想写自己的模板语言，我认为他们在处理 Dom 时会比我更快。
*   没有 jQuery——现在是 2019 年，没有理由再使用这个库了。
*   没有样式-我将允许用户对组件进行样式化。可能需要修改这个，这取决于难度有多大
*   编辑-我已经为莱德和 resharper 支付了 jet brains ultimate，所以我也可以利用他们的 web storm IDE。这和我感到舒服的事情非常相似。
*   框架支持-无。现在不感兴趣，也许以后我可以添加一些东西。
*   浏览器支持-最新或死亡！

# 感兴趣？

我也是！我不知道这将如何进行，也许我会在一个月内放弃，但我希望我能看到这一点，因为我认为这将有助于我自己和其他一些开发人员更容易的界面。

这里有一个项目 [GitHub 项目](https://github.com/joro550/bear-bones)的链接！

我会试着写一些关于想法和感受的博客，我哪里做错了，哪里做对了。我认为这对任何从我的工作中学习的人(包括我自己)来说都是一次很好的学习经历

## 名字怎么回事？

这是我在和某人谈论 devextreme 库的一个精简版本时犯的一个拼写错误，我喜欢这个名字，我打算为它弄一个:D 的标志

# 未来

*   图表！
*   任何我认为应该属于前端的东西

一旦我认为这个库的 JavaScript 版本稳定了，我将开始 blazor 实现，因为我认为这将是未来的发展方向，特别是对于 c#开发来说。我的一部分想从 blazor 方面开始，但我会成为一名优秀的开发人员，支持当前的技术，而不只是跳向闪亮的新技术。