# 繁忙远程的完整 Git rebase 流

> 原文：<https://dev.to/brylie/full-git-rebase-flow-for-a-busy-remote-56g5>

在阅读了几篇关于 Git rebasing 的文章和教程后，我在处理一个共享项目时没有了一些关键的上下文。有好几次，当我在一个过时的主分支上重定基础或者试图将我的重定基础的分支推到 GitHub 时，留下了一个混乱的提交图。下面是我没有使用的全流程 rebase 示例，这要感谢我的朋友和同事 Toni Ala-Piirto 的帮助。

**注意:**这个例子假设我们正在一个名为`origin`的**共享远程**中工作，并且我们正在将`master`分支重定为我们的`feature-branch`。

1.  确保本地`master`分支与最新的远程更改保持同步
    *   `git checkout master && git pull origin master`
2.  结账`feature branch`
    *   `git checkout feature-branch`
3.  重新确定基准并解决出现的任何冲突
    *   `git rebase master`
4.  *force* 将重设基础的分支推到远程(本例中为`origin`)
    *   `git push --force-with-lease`

有关`--force-with-lease`标志的更多信息，请参见 StackOverflow 问题[git push-force-with-lease vs . force](https://stackoverflow.com/a/52823955)。

* * *

[![Creative Commons License](img/e59cd93fa004ceb14ed402ec095ed201.png)](http://creativecommons.org/licenses/by/4.0/) 
本作品获得[知识共享署名 4.0 国际许可](http://creativecommons.org/licenses/by/4.0/)的许可。