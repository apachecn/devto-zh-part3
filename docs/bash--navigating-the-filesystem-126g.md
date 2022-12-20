# BASH:导航文件系统

> 原文：<https://dev.to/5anthosh/bash--navigating-the-filesystem-126g>

这篇文章最初发表在我的博客上，在这里找到原文

我们将了解如何使用 bash 命令在文件系统中导航。

首先，我们需要知道我们在文件系统中的位置。我们称那个目录为当前目录或工作目录。

要找出工作目录，我们可以使用`pwd`命令。

```
santhosh@ubuntu:~$ pwd
/home/santhosh 
```

> `pwd`的全称是打印工作目录。许多 bash 命令都是缩写。所以很好记。

通过使用`ls`命令，我们可以看到文件和文件夹在当前目录下。

```
santhosh@ubuntu-18-04:~$ ls Dev     Documents    Downloads 
```

> `ls`是 list 的简称。

我们还可以为 bash 命令提供参数，这使它更加强大。

要查看隐藏的文件，请使用`ls -a`

```
santhosh@ubuntu-18-04:~$ ls -a
Dev     Documents   Downloads .bash_history 
```

> 更多参数请参考`ls`的手册

我们可以使用`cd`命令在系统中移动。

```
santhosh@ubuntu-18-04:~$ cd Documents
santhosh@ubuntu-18-04:~/Documents$ _ 
```

移动到父目录

```
santhosh@ubuntu-18-04:~/Documents$ cd ..
santhosh@ubuntu-18-04:~$ _ 
```

> `..`(双点)是对父目录的引用。
> 
> `.`(单点)是对当前目录的引用。
> 
> `~`是指主目录。
> 
> `/`是对根目录的引用。

如果我们不带任何参数运行`cd`命令，我们将移动到主目录，而不考虑工作目录。

> 命令行可以用`tab`键自动完成我们的命令和路径。你可以自己试试。