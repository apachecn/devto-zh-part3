# 使用 Go 开始更大的项目

> 原文：<https://dev.to/jrwren/getting-started-on-bigger-projects-with-go-3m1e>

一个朋友最近告诉我，他的团队将使用 Go 重新开发一个相当大的后端。在这件事上，我有一堆(强烈和不强烈的)意见。所以我终于花时间写下我的想法，并意识到把它放到互联网上的某个地方是有意义的。

有一件事我很想写在邮件里，但我没有写，那就是我觉得 Go 变得越来越大，以至于有越来越多的这种带有坏建议的指导博客帖子。我希望这不是其中之一。事实上，我的第一个建议是:什么都不要做，除非你明白你为什么要这么做，如果你认为你知道你为什么要这么做，那么在编写 Go 代码的背景下，一定要知道你为什么要这么做。

我的简介是这样的:

这是矫枉过正:[https://github.com/golang-standards/project-layout](https://github.com/golang-standards/project-layout)并且实际上对重要的事情没有帮助，那就是:你不能在 Go 中有循环包引用，那么你如何分解它呢？幸运的是，大多数时候答案很简单:把所有东西放在一个包里，直到你有一个好的理由不这样做。但是不要因为它在其他语言中可能有意义就把事情分解成不同的包。例如 MVC——每一个都放在一个包中，直到提取一个模型在其他地方使用是有意义的，这时，应该提取一个描述相关模型功能的包，而不是一个模型包。

在[https://golang.org/doc/effective_go.html](https://golang.org/doc/effective_go.html)和[https://blog.golang.org](https://blog.golang.org)的一切都像是围棋的圣经。不要反对这些建议。全都是金色的。

这里有很多很棒的东西:https://github.com/golang/go/wiki，尤其是 https://github.com/golang/go/wiki/CodeReviewComments 的 T2 和 T3。

最后，请记住，接口不像其他语言中的接口，如果你带着接口和实现一切的 Java 思维，你将会给你自己和你的团队带来巨大的痛苦。

我可以详述很多，但我会等下一次。

祝你好运，我相信你会惊讶于你的结果和你对如此枯燥简单的语言和环境的热爱。