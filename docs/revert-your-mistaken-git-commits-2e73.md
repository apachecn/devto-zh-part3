# 回复您的错误 Git 提交

> 原文：<https://dev.to/walkingriver/revert-your-mistaken-git-commits-2e73>

最近，我的一个开发团队遇到了合并问题。对于版本控制，他们使用了修改后的 [GitFlow](https://nvie.com/posts/a-successful-git-branching-model/) ，是时候从`develop`分支合并到`release`分支了。如果事情做得正确，这应该总是一个干净，简单的合并。在这种情况下尤其如此，因为这是他们新项目的第一次发布。团队打开了一个从`develop`到`release`的拉请求，然后向我报告说 GitHub 报告了合并冲突。啊？这怎么可能呢？`release`分支应该是空的。但事实并非如此。

事实证明，团队中一个善意的开发人员知道代码最终需要进入`release`，所以他提交了所有的更改。我想锁定分支机构是改天的话题。我必须先解决眼前的问题。

我在`release`分支上运行了以下命令。

```
git log --oneline 
```

Enter fullscreen mode Exit fullscreen mode

它的输出显示如下:

```
1   852291a   blah blah blah
2   f575c87   blah blah
3   83d855d   blah blah
4   9fa11df   blah blah blah blah
5   111b003   blah blah
6   2b3a530   blah blah blah
7   a4c5f54   blah blah blah blah blah blah
8   b2a62fa   blah blah blah blah blah blah blah blah blah
9   5fb67b9   blah blah blah
10  4d1a5fc   blah blah blah
11  ed40aec   Initial commit 
```

Enter fullscreen mode Exit fullscreen mode

不，我们真的不用“废话”作为提交信息。正如我所说的，这些提交是直接对`release`分支进行的，而不是通过来自`develop`的合并或拉取请求，这是规定的方法。我对这个用心良苦的开发者没有任何怨恨。git 和这个工作流对团队来说都是新的，我主要责怪自己没有提前至少安排 30 分钟的讨论。他们对源代码管理并不陌生，只是这个特殊的过程。

不管我的感受或团队的良好意图，我面临着一个我必须解决的问题。从`develop`到`release`的新拉取请求正在等待。测试团队正在等待一个发布版本，但是没有发生。我认为最好和最安全的做法是撤销每一次提交。

所以在 Google 和 Bing-foo 搜索了一会儿之后，我在 StackOverflow.com 找到了一些问题和答案，这些问题和答案使我能够找到正确的 git 命令序列来修复`release`分支。我在这里记录下这段经历，这样我就知道下一次发生这种情况时去哪里找，希望它能帮助其他人。

我必须做的第一件事是弄清楚如何恢复一系列提交。只有 11 个，所以我可以一次做一个，但是如果有 111 个呢？不管提交的数量有多少，我都想理解这个过程。在回顾了各种可用的命令之后，我决定使用下面的语法:

```
git revert --no-edit <oldest-commit-hash>..<newest-commit-hash> 
```

Enter fullscreen mode Exit fullscreen mode

这实际上需要几次尝试，因为大多数文档都提到了“第一次错误提交”和“最后一次错误提交”“第一个”是最老的，还是日志中的第一个？结果，它是最老的，是提交日志中最高的数字。

因此，为了撤销上面列表中的所有提交，我使用了下面的命令:

```
git revert --no-edit ed40aec..852291a 
```

Enter fullscreen mode Exit fullscreen mode

`--no-edit`标志阻止 git 提示我为它所做的 11 次反向提交中的每一次编辑提交消息。相反，它使用最初的提交消息，前面有单词“Revert”我没意见。在该命令完成之后，我的每个提交都有一个镜像恢复，以相反的顺序。检查目录显示，唯一存在的是最初的 README.md 文件。完美！所以剩下唯一要做的事情就是将分支推回到服务器:

```
git push origin release 
```

Enter fullscreen mode Exit fullscreen mode

回到服务器，我检查了从`develop`到`release`的 open pull 请求，没有合并冲突。我完成了拉请求，构建可以继续了。

现在测试人员可以继续他们的工作了，我们都可以从此快乐地生活——当然，直到下一个问题。

* * *

*交叉贴自[走江湖博客](https://walkingriver.com/revert-your-mistaken-git-commits/)T3】*