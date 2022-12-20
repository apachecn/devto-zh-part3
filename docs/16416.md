# 快速说五遍:strace，ptrace，dtrace，dtruss

> 原文：<https://dev.to/captainsafia/say-this-five-times-fast-strace-ptrace-dtrace-dtruss-3e1b>

这篇博文最初是作为我对 Unix 命令的又一次深入研究开始的(还记得那些吗？)，在这里我深入研究了一个常见 Linux 命令的内部结构。我试图运行`strace`来确定我正在探索的命令调用的系统调用，这时我想起我在 macOS 上，macOS 没有`strace`，而是使用一个叫做`dtruss`的工具来跟踪程序执行期间调用的系统调用。

现在，我之前对`strace`和`dtruss`的区别一直比较无知。我所知道的是`dtruss`做了我需要它做的事情，我并没有太多的精力去探究它是如何工作或者它是什么的细节。

但是今天是脱去无知外衣的日子，朋友们！

### 什么是 strace？

是一个系统调用跟踪程序，也是技术领域中少数几个名字与其功能相符的东西之一。你可能对朱莉娅·埃文斯的杂志《T4》中的《T1》很熟悉。我认为 Julia 的杂志是了解`strace`的一个很好的途径，但这里是我对什么是 strace 的两点总结。

*   系统调用是一个接口，允许程序从操作系统请求一些功能。这些系统调用做一些事情，比如改变当前的工作目录，改变文件的权限等等。你可以在这里查看系统调用的完整列表。
*   列出程序执行时调用的系统调用。

该杂志没有涉及的一件事是`strace`如何在引擎盖下工作。我将在这里深入探讨。在幕后，`strace`利用了`ptrace`，它代表进程跟踪，一个允许父进程监视和控制子进程执行的系统调用。它在`strace`中使用，但它也支持类似`gdb`调试器的东西。`ptrace`系统调用使用一些内部的 Linux 数据结构来建立跟踪者(父进程)和被跟踪者(子进程)之间的关系。每当在被跟踪的进程中调用系统调用时，跟踪器将被通知该系统调用，并且被跟踪的进程将被暂时停止。此时，无论哪个程序正在调用`ptrace`，无论它是`strace`还是`gdb`，都将处理关于它被通知的系统调用的信息，然后将控制返回给子进程。这种在子进程 ptrace 和高级程序之间的来回跳跃凸显了`strace`的一个缺点。因为操作系统要在几个进程之间反复切换上下文，`strace`没那么快。

总的来说，`ptrace`在运行的流程和更高级别的工具如`gdb`或`strace`之间充当中介。

### 什么是 dtrace？

现在，这是我必须做一点研究的地方。我发现的关于`dtrace`的第一个定义是在[的 Brendan Gregg 的网站](http://www.brendangregg.com/dtrace.html)上，该网站将`dtrace`定义为“动态跟踪的实现”什么是动态跟踪？我不得不做了相当多的挖掘来找到一个很好地解释这一点的资源。最后，我看到了[的这篇文章](https://sysdig.com/blog/sysdig-vs-dtrace-vs-strace-a-technical-discussion/)，它帮助我了解了事情的真相。

鉴于`strace`依靠`ptrace`来反思过程，`dtrace`处理事情的方式略有不同。有了`dtrace`，程序员用一种叫做 d 的类似 C 语言的语法编写探针。这些探针定义了`dtrace`在调用系统调用、退出函数或任何你想做的事情时应该做什么。这些探测器存储在一个脚本文件中，如下所示。

```
syscall::read:entry {
    printf("read has been called.");
} 
```

该脚本声明，无论何时调用`read`系统调用，跟踪器都应该打印出字符串“read has called”然后用 dtrace 调用脚本文件，如下所示。

```
$ dtrace -s my_probe.d 
```

`dtrace`然后调用探针中的逻辑，只要它运行到该探针中概述的事件(进入某个系统调用或退出某个函数等等)。这种灵活性赋予了 DTrace 动态跟踪器的称号。

### 什么是 dtruss？

我着手揭开的下一件事是`dtruss`是什么。我遇到的第一个定义来自于`dtruss`联机帮助页，它将`dtruss`定义为“一个 DTrace 版本的 truss”好吧，我想我最好先弄清楚桁架是什么。事实证明，`truss`是一个特定于 Unix 的命令，它允许用户打印出程序发出的系统调用。它本质上是 Linux 上存在的`strace`工具的变种。知道了这一点，我想最好的描述方式就是用一个类比:`strace`之于`dtrace`就像`truss`之于`dtruss`。

### 还有哪些描摹工具存在？

现在，事实证明，`strace`和`dtrace`并不是我们的追踪器工具箱中仅有的工具。我的调查最终引导我探索更广阔的追踪者世界。事实证明，朱莉娅再次出手相救。布兰登·葛雷格[发表了另一篇博文](http://www.brendangregg.com/blog/2015-07-08/choosing-a-linux-tracer.html)，列出了不同的 Linux 追踪器，它们是如何工作的，以及何时可以使用它们。Brendan 似乎是这个领域的权威人物，已经出版了几本关于追踪的书，并写了许多不错的博客文章。如果你对这方面有兴趣，我推荐你看看他的一些博客文章。

### 结论

从冰山上滑下来多有趣啊。当你开始提出一个简单的问题(strace 和 DTrace 之间的区别)并最终发现更大的东西(一个全新的 tracers 世界)时，总是非常有趣的。

你经常使用哪种追踪器？你有没有特别喜欢的追踪工具？

#### 好奇者的链接

*   [挂在 DTrace 上](https://www.bignerdranch.com/blog/hooked-on-dtrace-part-1/)
*   [ptrace 在 Linux 中是如何工作的？](https://stackoverflow.com/a/23929059)