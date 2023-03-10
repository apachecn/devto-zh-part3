# VXer 最好的朋友:Delta Offset

> 原文：<https://dev.to/cpu/a-virus-writers-best-friend-the-delta-offset-1hle>

欢迎回来！如果这是你第一次访问 VeXation，你可能想先阅读一下我正在扩展的[项目](https://dev.to/cpu/vexation---lets-write-a-virus-51e0)、[开发环境](https://dev.to/cpu/getting-set-up-3kel)或正在进行的工作 [PE infector virus](https://dev.to/cpu/win95-file-infector-virus-basics-d8n) 。

# 重述

在[最后一篇](https://log.vexation.ca/2019/01/pe-file-infector-basics.html)的最后我完成了 [`minijector`](https://github.com/cpu/vexation/tree/cpu-pijector-wip/minijector) ，一个 PE 可执行文件感染病毒，它可以通过向被感染目标添加一个新的部分，将其代码添加到在同一目录下找到的`.exe`文件中。有一些缺点阻止`minijector`成为真正的功能性病毒。概括地说，病毒代码在 0:

1.  病毒代码依赖于不被复制到被感染程序中的数据段。变量引用都将被破坏。
2.  病毒代码使用 Win32 API 函数的方式将不起作用——间接层被破坏，第一个 API 函数调用将崩溃。
3.  病毒代码是惰性的。受感染程序的入口点不会被更新。

今天我将描述我解决这三个问题中的第一个问题的方法:使病毒自包含并且独立于位置。

# 代码和数据

Minijector 的一个大问题是它的`CODE`部分引用了单独的`DATA`部分中的变量。当 Minijector 的代码被复制到第 1 代+时，所有的变量都被留下，引用将无效！

我发现在可执行文件`minijector.exe`上使用`tdump`有助于获得这方面的直觉。