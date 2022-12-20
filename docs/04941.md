# 使用提交的一致提交

> 原文：<https://dev.to/lukehglazebrook/consistent-commits-using-commitlint-3hhj>

版本控制最难的部分是以一种对未来读者有意义且易于理解的方式来表示项目的历史。

创作提交消息既是一门科学，也是一门艺术。用于确保代码质量和标准得到维护的思想和时间也应该放在版本控制标准中。您应该能够回读提交日志，并清楚地知道随着时间的推移发生了什么变化。

> 如果您不能从您的版本控制日志中获得任何意义，那么历史本身就是没有意义的。

当处理 JavaScript/TypeScript 代码时，通常会有一个 linter 在你旁边工作，把你推向正确的方向。遵循林挺规则并不能确保你写出高质量、可读、无错误的代码，但它确实能帮助你思考这些事情，并在你工作的时候把你推向正确的方向。

如果你可以使用同样的林挺码概念，但不是 git 历史，会怎么样？

好消息是，你可以。介绍`commitlint`，一个在你的 VCS 历史中执行一些标准的简单方法。

但是等等，这实际上看起来像什么？通过推荐规则集的提交示例是什么？

`commitlint`背后的核心概念之一是给你的提交信息添加“类型”。你的下一个问题可能是，那看起来像什么？

```
docs: add contributing.md file
test: ensure hamburger menu is hidden on mobile
style: make Header component yellow by default 
```

Enter fullscreen mode Exit fullscreen mode

在上述情况下，`docs`、`test`、`style`为“类型”。他们的出现是为了以一致的方式向你提供更多关于公关内容的信息。

> 如果你想看到所有可用“类型”的列表，那么请查看 Github 页面的 [`commitlint`](https://wwww.github.com/conventional-changelog/commitlint) 。

使用`commitlint`还可以加强其他一些细节，比如主语是否可以为空，是否应该在一行的末尾有一个句号，主语应该是什么情况等等。

值得一提的是，就像代码林挺一样，单独提交林挺不是一个银弹。这不是你唯一需要给你和你的团队的好的、可读的历史。您仍然可以有大量完全遵循标准的提交，如下所示:

```
feat: add big feature
style: improve styling
docs: explain more stuff 
```

Enter fullscreen mode Exit fullscreen mode

然而，就像我们之前提到的——拥有一个 linter 是一个很好的方式来推动你自己(和你的同事)朝着正确的方向前进，并且唤起更多关于我们应该如何写我们的项目历史的思考。

到目前为止，我已经在一些个人项目和一些 OSS 项目中与`commitlint`一起工作过，我真的很喜欢它。如果你有兴趣给你的历史增加一些连贯性，我建议你去看看。

感谢阅读！如果你对我在这篇文章中谈到的任何事情有任何问题，请随时发推特给我