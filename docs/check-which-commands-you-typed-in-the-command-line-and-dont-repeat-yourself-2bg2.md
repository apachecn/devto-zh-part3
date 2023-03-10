# 检查你在命令行中输入了哪些命令，不要重复！

> 原文：<https://dev.to/flaviabastos/check-which-commands-you-typed-in-the-command-line-and-dont-repeat-yourself-2bg2>

最近，我需要在命令行中重复一系列很长的命令，但我从来没有完全记住它们，所以我最终找到了箭头向上-箭头向上-箭头向上*,直到我找到我正在寻找的命令。*

 *但是有一个更好的超级简单的方法:`history`

在命令行中输入`history`会列出所有最近输入的命令(我不确定有多远…)，所以你不需要记住任何东西。现在的问题是当你有一个很长的输出。你最终滚动-滚动-滚动 *< /ad 无限期*>*……*

在这种情况下，您可以使用`| grep`(竖线(|)，与键盘中的\键在同一个键上的东西)来限制结果。例如:我记得我想要的命令中有单词“prune ”,但是我不记得确切的命令，所以我运行:

`history | grep prune`

我将只得到包含单词“prune”的命令列表。

现在我有了完整的命令，我也可以节省一些打字。看到历史输出在命令列表前有一个数字了吗？

```
6050 docker ps6051 docker images6052 docker rmi c516053 docker ps -a6054 docker image ls6055 docker volume prune -f6056 docker system prune -a6057 docker images6058 docker ps 
```

如果你打字的话！命令号，该命令将被执行。从我上面的列表中，如果我输入

`!6055`

命令`docker volume prune -f`将再次运行。

我花了一段时间才开始使用这些快捷方式。直到我的历史变得非常长，我开始很少使用一些命令，以至于很难从记忆中回忆起来，我才明白搜索一个命令并调用它的快捷方式是多么有用。

> 帖子[检查你在命令行中输入了哪些命令，不要重复！](https://wp.me/pa0b0y-29)最初发布于 [flaviabastos.ca](https://flaviabastos.ca/)*