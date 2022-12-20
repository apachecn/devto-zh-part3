# 用特征测试记录遗留代码

> 原文：<https://dev.to/alexandra/document-legacy-code-with-characterization-tests-ejd>

我们被分配了遗留代码的工作。维护者已经不在了，我们也不知道系统应该如何运行。最安全的方法是什么？

首先，我们需要了解我们要修改的代码是做什么的。在我们做出更改之后，我们将结果状态与初始状态进行比较，以验证我们只引入了我们想要的更改。

我们可以通过特性测试获得初始系统行为的快照。特性测试有助于逆向工程规范，并且是系统行为的可靠来源。

下面是向遗留代码库添加特性测试的步骤。

[![🤷](img/6bffbbc34ca3bce93dec914d6e115f37.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--LxzOJpUz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11.2.0/72x72/1f937.png) **制定一个假设——**我们选择一个我们想要调查的单位或行为，并添加一个测试来测试它。

[![🙅](img/6e052567dce6d06c1cbb14145a52445f.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--21zYmRPC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11.2.0/72x72/1f645.png) **断言它——**我们故意写一个失败的断言，让测试输出告诉我们什么是正确的输出/行为。

[![🙋](img/437a30f7a38ad51b79fb5dd2eb2ea603.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--PFZU5DNS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11.2.0/72x72/1f64b.png) **清楚地了解系统—**测试会回溯我们的步骤，并且是可重复的。我们将来所做的任何更改都将与这个快照进行比较，这样我们就可以确定我们没有引入缺陷。

特性测试为其他需要与代码交互的人提供了文档，并最大限度地减少了分享专业知识的时间。

它们的实现也很有趣——这个过程有点像侦探工作，我们调查不同的假设来理解代码实际上做了什么。

当我们还在学习代码的时候，从一开始就在遗留代码库中实现特性测试是非常好的，并且它们甚至会在以后提供价值，因为它们支持重构工作而不会引入缺陷。