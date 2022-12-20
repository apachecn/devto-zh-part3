# 忽略漂亮

> 原文：<https://dev.to/clairecodes/ignoring-prettier-3pd1>

[更漂亮](https://prettier.io/)是一个“固执己见的代码格式化者”。[引用文件](https://prettier.io/docs/en/index.html):

> 它删除了所有原始样式，并确保所有输出的代码符合一致的样式。

它通常用于 JavaScript 项目，也提供支持其他语言的插件。漂亮可以通过它的 CLI 运行，甚至可以在每次点击 save 时通过你的代码编辑器自动运行。

让更漂亮的人来负责你的代码如何被格式化可以节省大量的时间，特别是在团队中工作时，代码评审可能会变成关于缩进和行长度的被动-主动的讨论。相反，你可以专注于你的代码做什么，而不是它看起来像什么。

## 如何让更漂亮的人忽略你的代码

然而，总有一种边缘情况:有一次你不同意 Prettier 如何布局你的代码。有没有可能在某些情况下关掉更漂亮？

是的有！有一整页的文档解释了如何。

有几种不同的方法可以关闭“更漂亮”。您可以:

*   忽略文件中的单行
*   忽略文件中的几行
*   忽略整个文件
    *   特定的一次性文件(如`my-template.html`)
    *   完整文件类型(如`*.html`)

根据问题的不同，可以通过更改更漂亮的配置来解决，而不仅仅是忽略代码部分。尾部逗号或双引号的首选项可以通过 [CLI 和 API 选项](https://prettier.io/docs/en/options.html)更改。然而，Prettier 特意提供了最少的可定制选项，因为该包背后的基本原理是消除许多关于代码风格的讨论和选择。

## 是否应该使用更漂亮的忽略选项？

使用 Prettier 的主要原因之一是将代码风格的决定权交给它。在对分号、尾随逗号等做出初始配置决定后，Prettier 处理与代码格式化相关的所有其他事情，即使您不喜欢该工具格式化几行代码的方式。

如果你设置 pretty 忽略一段代码以避开特定的格式样式，不要忘记你会失去 pretty 对这段代码的所有能力。在这个函数中，没有办法有选择地告诉 Prettier 忽略嵌套的三元语句的缩进，但仍然按照文件的其余部分强制括号间距。

如果你真的想让 Prettier 忽略一行代码，我建议在代码中留下注释解释原因。比如下面的代码，beauty 默认会去掉括号:

```
// Reason: more readable with brackets
// prettier-ignore
const totalThings = (widgets * 3) + (fizzbobs * 5); 
```

它可能无法通过团队其他成员的代码审查，但至少你给出了一个理由！

你有使用`prettier-ignore`的案例吗？还是顺其自然，让漂亮点的人来决定？

*Markus Spiske 在 Unsplash 上的标题图片。*