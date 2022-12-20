# 我想使用的 Git 接口

> 原文：<https://dev.to/revskill10/the-git-interface-that-i-want-to-use-3716>

这纯粹是一个想法，但我认为用`git`重新让编程变得有趣是有潜力的。

想象我们有一个命令`enter-git-mode`

我们的终端变成了`git mode`。它能做什么？

*   创建新文件夹。

在后台，git 将为我们创建新的分支，这样无论何时我们想要进入`cd foo/`，我们都在`foo`分支内。

*   移动文件。

现在在你编辑完代码后，你想把它提交给一个分支，只要使用著名的:`mv file foo/`，git 就会把保存的`file`提交给`foo`分支。

通常，我们需要一个`git mode`来将我们的文件夹命令映射到 git 命令。

希望这是一个有趣的黑客周末的想法。