# GitHub 的 8 个生产力技巧

> 原文：<https://dev.to/_darrenburns/8-productivity-tips-for-github-44kn>

随着最近[无限私有库](https://blog.github.com/2019-01-07-new-year-new-github/)的发布，让我们花几分钟时间，在我们推出不想让其他人看到的代码之前，确保我们充分利用了 GitHub 所提供的功能。

GitHub 是用一些非常有用的快捷方式和提高生产力的特性构建的。然而，从个人经验来看，很明显这些经常被开发人员忽略。如果我曾经目睹过 GitHub 的某个特定功能给某人带来惊喜或帮助某人，那么这个功能就在这个页面上。也就是说，以下内容绝不是一份详尽的清单。

## 在存储库中快速模糊搜索文件

毫无疑问，当你知道你在找什么的时候，这是浏览存储库最快的方法。打开任意存储库并按下`t`。现在，您可以搜索存储库中任何文件的名称，并使用键盘上的箭头键浏览结果。按 Enter 键打开文件。

[![github-fuzzy-search](img/519ba9c718d68aeb774070ae3edb8299.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t7GLDTFR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ncpvaeb7t8yhum3ej5ht.gif)

## 拉取请求中的代码变更建议

当对拉请求中的一段代码进行注释时，您可以使用“建议的更改”功能来建议替代代码。pull 请求的作者将能够在不离开 GitHub 的情况下立即应用您的建议。

为了给出建议，用多行 Markdown 代码片段包围代码片段，标记为`suggestion`:

[![gh-make-suggestion](img/01de2e4667354e102bc6997b7ca6c95b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N4_nzfO7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4naqtovc61o8mhk7kl81.gif)

既然您已经提出了建议，拉请求的作者可以立即将它应用到他们的分支，而不需要手动更改文件！

[![gh-apply-suggestion](img/9ace76754cebad199205a21743688f15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zx7kREyW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ndr472k33i3ilvkvihxp.gif)

## 像在 IDE 中一样浏览代码树

这需要一个非官方的 Chrome 扩展，但与默认界面相比，这是一种更熟悉的导航代码的方式。八叉树扩展允许你浏览 GitHub 库，带有侧边栏树视图，类似于你在 VS Code 等应用程序中得到的。

[![octotree](img/6e02268c3c2a461bb669d3c55bea7cbc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HFKQwuVV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zrnjj9ejzy3xk5b6mhch.gif)

## 查看代码时跳转到一个函数

除非您正在审查一个单独的函数，否则代码审查通常会涉及到函数调用和它们的定义之间的大量跳转(因此需要大量的上下滚动)。GitHub 允许你在查看一个 pull 请求中的文件时，通过按`t`跳转到一个符号。

[![gh-jump-to-symbol](img/0eadbf16757328832485f1123417d482.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MmUaYoxu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z2snvbftcsc52l63cdvo.gif)

## 创建文件的永久链接

当查看文件或目录时，按下`y`，URL 将被转换为永久链接，您可以共享该链接，因为您知道文件的内容永远不会改变。

如果你在 GitHub 上发送一个指向文件或目录的链接，而没有把它变成一个永久链接，你需要接受这个文件明天可能会消失，断开链接的可能性！

## 查看责备和更改新近度热图

当查看一个文件时，你可以按下`b`来查看 Git 错误和显示最近每一行被修改的热图。它会告诉你谁最近修改了每一行代码，并给你一个可点击的链接，带你到完整的提交，这是修改的一部分。

在装订线(包含提交消息和作者)的右边，您会注意到一个橙色的竖线。这个条越生动，最近的变化就越多，这意味着您可以轻松地扫描文件以找到最新的代码！

[![gh-blame](img/8c3fb13b9951185c15f7afce7114331f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B0RjJg2u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w6krhgeok66xk35th9lz.gif)

## 强大的代码搜索

GitHub 索引了大多数代码，并提供了强大的搜索功能。如果您需要在存储库中找到一些东西，但是不打算对它进行任何更改。通常不需要签出存储库。按下`/`键搜索存储库中的所有代码。

[![gh-code-search](img/ad7be6f2fcb114459b3cc93132b3ec24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eB9ezBIv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2vowf0suy4486bv44nhq.gif)

如果您的搜索包含多个单词，并且您想要搜索特定搜索查询的匹配项，请在查询两边加上引号。你也可以通过其他东西来过滤你的搜索，比如文件大小、扩展名、文件所在的路径等等。

## 已保存回复

如果你发现自己重复同样的评论，你可以通过创建一个[保存回复](https://github.com/settings/replies)来节省一些时间。下一次你发现你要再次输入注释，你可以从下拉菜单中选择:

[![gh-saved-replies](img/5d3f441783b5e9406251f098855eabf3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3vPGdaE7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hw6kg3wv1cw0ll80oy7u.gif)

要在不使用鼠标的情况下执行上述操作，我可以先执行`ctrl + .`，然后再执行`ctrl + 1`。

## 结论

感谢阅读。我希望你在这个页面上至少找到了一件能让你成为更有效率的 GitHub 用户的事情。如果你喜欢这篇文章或者有任何反馈，请告诉我！

如果你对更多类似的内容感兴趣，请关注我的推特。

*最初发表在我的[博客](https://darrenburns.net/posts/github-tips)上。*

*附:你可以制作你自己的章鱼来分享，就像在[myoctocat.com](https://myoctocat.com)的封面照片中的那只！*