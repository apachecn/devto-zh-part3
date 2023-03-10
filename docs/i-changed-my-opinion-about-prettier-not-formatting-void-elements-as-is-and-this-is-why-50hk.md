# 我改变了我对漂亮不格式化空元素的看法，这就是原因

> 原文：<https://dev.to/michaeljota/i-changed-my-opinion-about-prettier-not-formatting-void-elements-as-is-and-this-is-why-50hk>

# 简介

Prettier 是目前你能拥有的最好的格式化程序，它目前支持广泛的 web 编程语言，JavaScript，Typescript，Flow，JSX，JSON，CSS，SCSS，Less，GraphQL，Markdown，YAML，最近它还增加了对模板语言的支持，HTML，Vue 和 Angular。

我真的被这个特性所吸引，并且在 PR 开放时帮助测试并给出早期反馈。测试我非常想要的东西，并帮助它成形，这很酷。虽然，从我第一次尝试开始就有一些问题困扰着我:void 元素被改成了自封闭元素。

# Void 元素 vs 自闭元素问题。

这一点已经被广泛讨论过了，prettle 团队的一个主要论点是自结束元素是 XML 兼容的，但是 prettle 本身并不支持 XML。自从我注意到，我试图强烈地表达我对此的看法，毕竟，Prettier 是一个固执己见的代码格式化程序。我发起一个问题来支持一个选项，因为我反对这种行为。几个关于 HTML 的指南风格推荐按原样表达 void 元素，因为毕竟结束标签会被浏览器忽略，它可能会导致潜在的恼人的，但实际上不太可能的错误。

# 然后我用它

然而，因为是我推动我的团队使用 Prettier 作为格式化程序，所以我继续进行升级。差不多 5 个月前了，版本 1.15。在 5 个月的时间里，我每天都与模板和组件打交道，我不得不说:我没有注意到它。

我只是使用了格式化程序，然后就忘记了。我不记得它是无效的，还是自我封闭的，到目前为止我还没有发现任何明显的不当行为。浏览器实际上忽略了结束标记，仅此而已。

然而，自动格式化程序给我的工作带来了很多好处。我几乎没有花时间上下移动属性，尝试让它们适合一行，或者在我想的时候给它们一个新行。所有的文件都有完全相同的格式，阅读 HTML 变得非常容易。

这就是胜利。当你有一个稳定的资源基础时，这些小细节并不重要。你可以添加任何数字或属性，漂亮将管理如何处理它们。

# 试一试

我在这期杂志上看到有人抱怨结束标签伤害了他们的眼睛，或者因为他们的团队不接受这一点，因为他们的风格指南给出了相反的建议，这篇文章是给你们所有人的:帮自己一个忙，用现在更漂亮的。几个月后你就不会注意到了。我保证。你会收获很多，为了这么少而妥协，这是值得的。

这适用于新行中的结束标记。有趣的是，JSX 选项是由脸书添加的，因为他们用另一种方式更漂亮的是在那一刻这样做。

# 结束

就这些了。这只是一个关于我如何改变对 HTML 格式美观的看法的快速帖子。我希望你喜欢它。我保证从现在开始我会多写一点。

[![](img/105f52ace612b5626d458b64e7b3df26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tNe0WzMa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/utq3v8xram83pyz8nlw3.gif)
[![](img/e9e7ab34050d31a3416896afc82c2dd1.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--ewRlt6Ky--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6snht105xrk7kkvneaiv.gif)

PS:如果你好奇，我在这里列举的问题是:[漂亮#5246](https://github.com/prettier/prettier/issues/5246) 和[漂亮#5377](https://github.com/prettier/prettier/issues/5377) 。但是关于 HTML 格式的当前样式还有更多问题。