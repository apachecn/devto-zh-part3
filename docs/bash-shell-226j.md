# BASH SHELL

> 原文：<https://dev.to/5anthosh/bash-shell-226j>

这篇文章最初发表在我的博客上，在这里找到原文

## 什么是壳？

计算机理解二进制语言。在计算的早期，指令是用难以读写的二进制语言提供的。所以，为了使指令成为可读可理解的格式，在操作系统中有一个特殊的程序叫做 [shell](https://en.wikipedia.org/wiki/Shell_%28computing%29) 。外壳接受人类可读的命令，并翻译成内核可以读取和处理的指令。

外壳是为用户交互提供的用户程序或环境。命令语言拦截器执行来自标准输入设备(如键盘)或文件的命令。当我们登录或打开终端(控制台)时，shell 就会启动。

最常见的 Linux shell 被命名为“ **Bash shell** ”。

## 痛击炮弹

bash shell 的灵感来源于 [**伯恩 shell**](https://en.wikipedia.org/wiki/Bourne_shell) 。它是 Linux 系统中最重要的软件之一。

linux 系统中打开终端的快捷方式是`CTRL + ALT + T`。

当我们打开终端时，它会提示我们输入命令。`santhosh`是用户名，`ubuntu-18-04`是我的系统名。

```
santhosh@ubuntu-18-04:~$  _ 
```

> `~`在提示符下显示我们在用户主目录下。
> 如果我是 root，那么我的主目录将是`/`。

找出我们正在使用的 shell。

使用`help`命令打印外壳名称及其版本

```
santhosh@ubuntu-18-04:~$ help GNU bash, version 4.3.48(1)-release (x86_64-pc-linux-gnu)
These shell commands are defined internally.  Type `help' to see this list.
Type `help name' to find out more about the function `name'.
Use `info bash' to find out more about the shell in general.
Use `man -k' or `info' to find out more about commands not in this list. 
```

或者可以打印[系统变量](https://en.wikipedia.org/wiki/Environment_variable) `SHELL`得到外壳

```
santhosh@ubuntu-18-04:~$ echo $SHELL
/bin/bash 
```

`echo`是用于显示消息或系统变量的命令。

```
santhosh@ubuntu-18-04:~$ echo message
message 
```

> 当我们输入一个命令时，它实际上存储在历史记录中(主目录中的`.bash_history`文件)，所以我们不必重新输入以前输入的命令，我们可以使用向上⬆️和⬇️向下箭头键遍历这个历史记录。

我们可以使用简单的`whoami`命令显示用户的用户名。

```
santhosh@ubuntu-18-04:~$ whoami santhosh 
```

我们可以使用`man`
获得文档
或命令手册

```
santhosh@ubuntu-18-04:~$ man whoami 
```

> 要退出手册页，请按`q`。