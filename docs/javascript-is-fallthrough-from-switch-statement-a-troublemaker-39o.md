# JavaScript:Switch 语句的 Fallthrough 是个麻烦制造者吗？

> 原文：<https://dev.to/arisa_dev/javascript-is-fallthrough-from-switch-statement-a-troublemaker-39o>

*从我的媒介交叉发布。
[中:阿里萨 F](https://medium.com/@shabibi/javascript-is-fallthrough-from-switch-statement-a-troublemaker-90597853d4a8)

大家好，我是来自日本的 Arisa，在德国工作和生活，是一名全栈开发人员。

我从前端领域开始我的职业生涯。每个人在前端领域必须学习的编程语言是 JavaScript。

我在一所在线编程学校 CodeGrit 教书，有时，我的学生会提出一些非常有趣的问题或讨论，让我大开眼界。

我的学生最近提出的一个有趣的讨论是关于 switch 语句的失败。

我个人避免在 switch 语句中使用 fallthrough，但从他和其他人的角度来看，这很有趣，让我想讨论一下利弊以及更多。

我们开始讨论吧。

# fall through 允许使用吗？

一个简短的回答是肯定的。

它甚至在某些情况下也用在像 MDN 这样的文档中。此外，我们没有发现一个不允许在任何地方使用的句子。

你可以用正确的方式自由使用！

那为什么有些人和我一样尽量不在 switch 语句中使用 fallthrough 呢？有一些你必须知道的使用穿越权的理由。
表示使用 fallthrough，风险自担。

这听起来有点夸张…对他们中的一些人来说。你是否介意冒险完全取决于你自己。此外，当你遵循语法并在有效的场合使用它是完全没问题的。

那为什么有些人格外小心呢？

这里还有一个讨论。

# 是个危险的家伙？

我会说，fallthrough 是一个非常好的人，但同时，在某些场合，他也是一个危险的人。

在这种情况下失败是件好事。

*   许多情况下，比如超过 10 个不需要中断的情况
*   许多案例和非常简单的输出返回

看到真实的例子就比较好理解了。让我们来看看这个例子。