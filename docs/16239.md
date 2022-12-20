# 用 git-等分搜索 bug

> 原文：<https://dev.to/easyaspython/hunt-bugs-with-git-bisect-l44>

我最近在我们的网站上实现了一个“粘性工具栏”功能。这是我第一次从现有的 Vue 组件中为页面的其余部分创作新的东西，非常有趣。工具栏的功能组合在一起，除了 CSS 之外，不需要做太多工作就能得到正确的定位。当我最终合并这些代码时，我感觉非常好。

一天后，我在一天快结束的时候浏览这个网站，发现了一个问题。该样式位于工具栏的一个下拉菜单中。我很自然地担心是我最近的改变导致了这个问题，而且不知何故我错过了它。我检查了一下`master`上的最新提交确实在本地展示了这个问题，果然如此。在我的更改之后，又出现了一些其他的更改，所以我检查了我的更改的最后一次提交。我运行构建并刷新页面，但是一切看起来都很好。

我很欣慰没有导致回归，但这意味着我不知道错误是从哪里引入的。我花了整整 30 分钟检查 Chrome DevTools 中的元素和 CSS，试图了解在想起有更好的方法之前发生了什么变化。git-平分救援！

git-bi section 是一个通过提交历史运行二分搜索法的工具。它允许您放置将特定提交标记为好或坏的标杆，逐渐缩小搜索空间，直到到达引入更改的提交。最常见的情况是，您会启动一个 git-sector 会话，并将最近一次提交标记为坏的，因为您已经知道那里有一个问题:

```
$ git bisect start
$ git bisect bad 
```

下一步是找出过去 bug *不在*的时间。
我已经发现*我的*最近提交是正常的，所以我从那里开始:

```
$ git bisect good a56f1b2  # The SHA of my latest commit
Bisecting: 24 revisions left to test after this (roughly 5 steps)
[ab6c7a77d...] Add social media icons (#197) 
```

神奇的事情就发生在这里。Git 现在知道最近的提交有一个问题，并且在过去这个问题还不存在。我已经设置了搜索空间的边界，Git 将我放到了我指定的两个边界中间的 commit 上。

`✅ . . . . . . . . . . . . v . . . . . . . . . . . . 💔`

现在我检查:这个问题存在吗？不。我可以告诉 Git:

```
$ git bisect good
Bisecting: 12 revisions left to test after this (roughly 4 steps)
[bb8c65a43...] Increase contrast for small text (#203) 
```

Git 已经把搜索空间切了一半，又把我放在中间了。

`✅✅✅✅✅✅✅✅✅✅✅✅✅✅ . . . . . . v . . . . . . 💔`

这里存在问题吗？哦，是的！我让 Git 知道:

```
$ git bisect bad
Bisecting: 6 revisions left to test after this (roughly 3 steps)
[97e12fab6...] Improve exception logging (#202) 
```

我们越来越接近了。

`✅✅✅✅✅✅✅✅✅✅✅✅✅✅ . . . v . . . 💔💔💔💔💔💔💔💔`

再经过几个步骤，我终于剩下一个提交，Git 给出了这个特定提交的信息，让我知道:

```
$ git bisect bad
6c3853827... is the first bad commit
commit 6c3853827...
Author: Arthur Q. Dev
Date:   Thu Jan 10 16:11:12 2018 -0500

    Update shared menu styling (#199) 
```

`✅✅✅✅✅✅✅✅✅✅✅✅✅✅✅✅✅💥💔💔💔💔💔💔💔💔💔💔💔`

我现在可以精确地看到*在提交过程中发生了什么变化，通常可以更容易地看到是什么特定的代码变化导致了 bug 的发生。这些好的/坏的步骤只需要测试来确定问题是否存在，所以整个 git-等分会话只需要几分钟。一旦你发现了令人不快的提交，你就不必再去猜测可能会发生什么——你可以直接看到变化，并批判性地思考它。*

除了 bug 之外，您还可以使用 git-bi section。有时候你只是想知道什么时候*改变了*。git-bi section 本身也理解`git bisect [old|new]`，但是如果你在追踪不同的东西或者喜欢不同的术语，你可以使用`--term-[old|new]`将它们传递给`git bisect start`。

我并不总是使用 git-等分，但是，当我记得的时候，我喜欢它。查看[完整文档](https://git-scm.com/docs/git-bisect)并充分摇动该工具。