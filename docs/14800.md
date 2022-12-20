# 用 gdb + php5-dbg 窥探 PHP 进程

> 原文：<https://dev.to/shaikhul/spy-php-process-with-gdb--php5-dbg-16l9>

在我之前的[帖子](https://dev.to/shaikhul/how-to-spy-a-process--3p2n)中，我简单提到了用 gdb 调试 php 进程。在这篇文章中，我将描述从一个正在运行的 PHP 进程中成功获取 insights/stacktrace 的步骤和所需工具。

首先，确保你已经安装了 php5-dbg 包。在这个例子中，我使用的是 Ubuntu 盒子。

```
$ dpkg-query -l | grep php5-dbg 
```

如果没有找到，首先安装它

```
$ sudo apt-get install php5-dbg 
```

我们还需要一个`.gdbinit`文件。根据 php 版本从[这里](https://github.com/php/php-src/blob/PHP-5.3/.gdbinit)获取。

现在是时候用 php 进程启动 gdb 了。

```
$ sudo gdb -p PID 
```

当 gdb 暂停程序时，你需要在 gdb shell 中输入`continue`。

```
> cont 
```

等待一段时间，然后按 Ctrl+C 返回到 gdb shell。

现在，我将`.gdbinit`放入我的主目录。

```
> source ~/.gdbinit 
```

键入`zbacktrace`查看来自底层 php 程序的实际回溯。

```
> zbacktrace 
```

获得堆栈跟踪后，从 gdb 中分离程序，然后退出，如果你不分离程序，当你退出 gdb 时，gdb 会杀死它。

```
> detach
> q 
```

您可以检查`.gdbinit`文件中可用的其他命令。

这帮助我今天获得了 php 程序的实际堆栈跟踪，而不是我用`bt`命令获得的 php 解释器调用。

`gdb`真的是超赞的酱！