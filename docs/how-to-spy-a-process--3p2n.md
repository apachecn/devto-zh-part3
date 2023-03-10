# 如何窥探一个进程

> 原文：<https://dev.to/shaikhul/how-to-spy-a-process--3p2n>

你有没有遇到过一个程序长时间运行不动，或者不知道这个进程在做什么的情况？别担心！你不是一个人，同样的事情在我身上发生了很多。

最近，我不得不监视一个长时间运行的 php 进程，以找出为什么/在哪里花费了这么多时间。

以下是我在 linux 环境下窥探任何进程的方法。

通过运行特定程序的 [ps](https://linux.die.net/man/1/ps) 命令和 [grep](https://linux.die.net/man/1/grep) 来找出进程 ID。在我的例子中，它是一个 php 程序，所以我输入如下:

```
$ ps aux | grep "some_program.php" 
```

Enter fullscreen mode Exit fullscreen mode

然后[跟踪](https://linux.die.net/man/1/strace)这个过程。您需要 root 访问权限。

```
$ strace -p PID 
```

Enter fullscreen mode Exit fullscreen mode

如果它在标准输出中转储了很多，也不用担心。它跟踪进程正在进行的所有系统调用(`read`、`write`、`open`、`close`、`poll`、`recvfrom`等)。寻找任何有趣的模式。

要查看进程打开了哪些文件，您可以尝试 [lsof](https://linux.die.net/man/8/lsof) 命令。这有助于我找出程序使用了哪些文件路径。

```
$ lsof -p PID 
```

Enter fullscreen mode Exit fullscreen mode

要进一步调试该流程，请尝试 [gdb](https://linux.die.net/man/1/gdb) 。
首先，把程序附在我行。

```
$ gdb -p PID 
```

Enter fullscreen mode Exit fullscreen mode

现在你在广发壳。键入 cont 并等待一会儿。然后按 ctrl + c 停止它。

```
> cont 
```

Enter fullscreen mode Exit fullscreen mode

它将停止并返回到 gdb shell。现在键入`bt`它将转储回溯。可以看到一些 php 库函数调用。

```
> bt 
```

Enter fullscreen mode Exit fullscreen mode

这几个命令非常有助于我深入了解一个进程，并弄清楚发生了什么。当您不太了解底层代码库时，这尤其有用。

要了解更多关于 strace 的信息，请阅读下面两篇文章。

*   朱莉娅·埃文斯撰写的杂志
*   php 在做什么？吴镇男·雷瑟斯著