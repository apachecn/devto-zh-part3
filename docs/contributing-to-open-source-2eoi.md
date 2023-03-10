# 为开源做贡献...

> 原文：<https://dev.to/raerpo/contributing-to-open-source-2eoi>

为开源做贡献是我最喜欢做的事情之一。能够为一个帮助其他开发人员的项目做贡献，并且有机会与那些你可以向其学习的人一起工作，这种感觉很好。

我在我以前工作中使用的一个库中打开了一个 [Github 问题](https://github.com/nickpisacane/react-dynamic-swiper/issues/25),我很乐意向你展示我如何通过贡献代码来修复它的过程，也许希望激励你为这个或其他项目做出自己的贡献。

这个项目叫做 [react-dynamic-swiper](https://github.com/nickpisacane/react-dynamic-swiper) ，它是一个 react 包装器，包裹着令人敬畏的 iDangerous swiper 库。

## 识别问题

有两种方法可以找到问题并开始修复。

1)您在正在使用的库中发现了一个问题，并注意到其他任何人都没有报告过该问题。

2)你可以在项目中找到带有类似`help wanted`或`low hanging fruit`标签的未决问题，即使你自己没有经历过这些问题。这个[页面](http://github-help-wanted.com/)可以帮你发现问题。

在`react-dynamic-swipper`的例子中，我注意到它有一个缺失的特性，所以我开了一个问题，问作者这是不是一个错误，或者这个特性是否没有包含在图书馆的路线图中。

重要的是要找到问题是否已经被报告，或者其他人是否正在寻找解决方案。当你花时间研究一个解决方案，却发现别人先发表了一个，甚至更糟的是，这个问题根本就不存在，这真是令人沮丧。

[https://github . com/nickpisacane/react-dynamic-swiper/issues/25 # issue-429471387](https://github.com/nickpisacane/react-dynamic-swiper/issues/25#issue-429471387)

几天后，作者确认这实际上是一个缺失的特性，如果我需要它，我可以发出一个拉请求来解决这个问题。

## 准备阶段

现在问题已经分配给你了，你可以开始处理它了。第一步是做一个项目的分叉。

要创建一个 fork，你应该使用位于项目 github 页面的`Fork`按钮。

[![Github fork button](img/9286f121ce4a05ebbaedd3a29449317a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XC_vKgQ1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ir7tqfkuug9eyv7a2lct.png)

当你点击 fork 按钮时，Github 会带你到同一个项目，但是是在你的用户名下。现在你可以[将它克隆到你的机器](https://help.github.com/en/articles/cloning-a-repository)中。

转到您最近创建的项目，创建一个新的分支，现在您就可以对代码进行更改了。

## 做实际改变

这一部分是最难解释的，因为每个案例都不一样。这种变化可能是文档中的一个打字错误(这种变化对项目维护者来说非常重要)，可能是添加了一个新的特性，甚至是完全重写。

在这一步，我不能帮你太多，但我可以给你一些建议，这些建议适用于每一种类型的贡献。

*   尽你最大的努力按照项目的贡献准则做出改变。如果项目没有，您可以通过添加它来做出贡献。

*   当你有一个提议时，即使它还没有完成，打开一个 PR 表明你需要一个维护者的意见。

*   虚心接受批评，请不要以个人的方式看待他人的评论。如果他们要求修改，考虑他们。变化是，他们比你更了解这个项目，所以把这当成一个了解这个项目的机会。

这是我为这个库提议的[变更。](https://github.com/nickpisacane/react-dynamic-swiper/pull/27)

## 让拉取请求等待被包含

这部分可能很快，也可能需要很长时间，这取决于项目。有时你的 PR 根本不能包含它，因为它不是维护者同意的东西(这很糟糕，我知道:()。

请保持耐心，避免发表评论来催促维护人员合并您的更改。

## 利润

一旦你的贡献被包括进来，你现在就是这个项目的一部分，你可以为你帮助构建了一些其他人使用的东西而感到自豪。

## 接下来是什么？

为开源做贡献是提高你的技能同时帮助别人的一个很好的方式。很多人可以通过致力于改进开源库来获得很棒的工作。

此外，一旦你开始做，你总是觉得有必要做得更多，所以这是一个双赢的局面。

如果您想更深入地了解如何为开源项目做贡献，可以从一些很棒的资源开始:

[https://git-SCM . com/book/en/v2/GitHub-contribute-to-a-Project](https://git-scm.com/book/en/v2/GitHub-Contributing-to-a-Project)
[https://egghead . io/courses/how-to-contribute-a-open-source-Project-on-GitHub](https://egghead.io/courses/how-to-contribute-to-an-open-source-project-on-github)