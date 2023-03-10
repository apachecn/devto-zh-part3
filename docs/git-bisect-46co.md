# 吉毕斯

> 原文：<https://dev.to/apium_hub/git-bisect-46co>

Git 为[软件开发](https://dev.to/)提供了大量真正有用的工具，值得探索。今天我将谈论其中的一个: **Git 等分**，当我们想要在代码中搜索特定的东西时，它非常有用。

## 什么是 Git 等分，它是如何工作的？

二分法来自二分搜索法算法，它是一种搜索大量(排序)数据的有效方法。通过重复地将批处理分成两半并执行某种验证检查，我们能够在有限的迭代次数中扫描大量的数据。可以通过简单地“检查”特定的提交并查看代码来手动执行二等分。然而，提供的实现保护我们免受各种可能的错误，并为我们做了大量的体力劳动。

什么时候用等分比较好？当您不确定代码中何时发生了特定的变化时。当它被引入时，可能是一个很难跟踪的错误，不需要的改变，就像一个代码删除被错误地删除了。在所有这些情况下，等分是很方便的。

[![GSAS](img/2dbe1733eacd9e3c7573ae14c6e4cb61.png)](https://gsas.io)

出于举例的目的，让我们简单地说我们有一个 bug。很难跟踪最初是哪个变化导致了它，但我们可以肯定的是，一周前它还不在那里！太好了，我们开始对分吧。

在你开始平分之前，请用 commit 或 stash 保存你未完成的工作！

首先，我们需要初始化搜索，用:

```
git bisect start 
```

现在我们需要用*好的*和*坏的*标签来标记两个提交，以指定搜索的边界。我将把当前的磁头标记为坏的，因为这个 bug 现在就可以重现:

```
git bisect bad 
```

接下来，当我们确定一切都还正常时，我们需要及时标记这个地方。同样，它可以用提交的 SHA、标签或分支来指定，或者简单地通过检查一个特定的提交并将其标记为好:

```
git checkout 1234567

git bisect good 
```

或者

```
git bisect good 1234567 
```

从现在开始，Git 将开始在两次提交之间移动头部，为我们提供了一种验证代码状态的可能性。这些说明不言自明；我们可能会看到这样的东西:

```
Bisecting: 191 revisions left to test after this (roughly 8 steps)

[commit_123] Add new transaction endpoint 
```

我们的任务是验证代码，无论是编译和运行应用程序，还是针对给定的问题启动测试用例。一切都取决于具体情况。Git 将让我们浏览历史，逐步优化我们需要执行的验证数量。我们的工作只是让 git 知道在那个时间点代码仍然是“好的”还是已经是“坏的”——“Git 平分好的”还是“Git 平分坏的”。Git 会自动跳到下一个需要我们陈述判断的候选人:

```
git bisect good

Bisecting: 95 revisions left to test after this (roughly 7 steps)

[commit_321] Replace the User model with new implementation 
```

在指定数量的选择之后，我们将被提示可疑提交及其所有信息。

最后，别忘了“重置”。也不要犹豫重置，在任何给定的时刻，万一出了问题，你希望重新开始。因此:

```
git bisect reset 
```

是你优雅地完成算法所需要的。

以防您忘记这样做，Git 将所有必要的数据存储在。您的存储库的根目录中的 git 目录。从那里删除所有的`BISECT_`文件可能会修复你可能遇到的大部分问题。

```
rm .git/BISECT_* 
```

好吧，让我们稍微回顾一下。打开一个终端，如果你还没有这样做的话，`cd`自己去找一些你碰巧在附近的 git repo，让我们来练习一下。

首先，检查是否有任何挂起的更改，并提交或隐藏它们。然后这么做:

```
git rev-list —max-parents=0 HEAD 
```

这将为您提供回购的初始提交 SHA。

你可能想简单地一分为二开始或开始通过坏的和好的点(按此顺序)。试试这个:

```
git bisect start first_commits_sha_number_here HEAD 
```

您应该会看到一条失败消息，这是有意义的，对吗？你为什么要找一个你已经做好的地方呢？

等分起点可以接受一个或两个修订参数。只是坏的，或者坏的和好的，所以让我们来解决它:

```
git bisect start HEAD first_commits_sha_number_here 
```

头不好。第一次提交很好。

现在:

```
`git bisect good/bad` 
```

你也可以试试`git bisect next`或者`git bisect skip`。这意味着允许您在无法明确验证的情况下传递提交。

结束后，尝试:

```
git bisect log 
```

从头再看一遍

```
git bisect run 
```

回复一下这个趣事，这里值得一提的是“运行”可能会接受一个脚本来自动验证代码。

也试试:

```
git bisect visualise 
```

这将打开您的默认可视化工具。试着用它做实验，在对分的中间。

好了，最后一招。我可能应该从这个开始，但这会破坏我向你解释这一切的乐趣。对于那些还没有跳到控制台的人来说，这是你们最后的机会——就:

```
git bisect 
```

给你一个方便的提醒:

```
usage: git bisect [help|start|bad|good|skip|next|reset|visualize|replay|log|run] 
```

### 总结一下:

平分是一种易于使用的搜索算法，它允许我们在相当短的时间内扫描甚至一个巨大的历史。它也是非侵入性的和相对简单的。只要记住总是从一个干净的工作目录开始，并在完成后重置它！当你需要搜索你的历史时，随时可以使用它。

当然，完整的手册可以通过输入:`git help bisect`获得。

### 参考文献:

*   [https://git-scm.com](https://git-scm.com/)
*   [https://github.com/git/git](https://github.com/git/git)
*   [https://git-SCM . com/book/en/v2](https://git-scm.com/book/en/v2%E2%80%9D%20target=)
*   [https://stack overflow . com/questions/5188914/how-to-show-first-commit-by-git-log](https://stackoverflow.com/questions/5188914/how-to-show-first-commit-by-git-log)

帖子 [Git 平分](https://apiumhub.com/tech-blog-barcelona/git-bisect/)最早出现在 [Apiumhub](https://apiumhub.com) 上。