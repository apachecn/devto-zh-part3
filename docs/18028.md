# 现在是晚上 10:37，我的文件表溢出来了。

> 原文：<https://dev.to/captainsafia/its-1037pm-and-my-file-table-just-overflowed-h01>

因此，我最近在一个 lerna 管理的 monorepo 上运行构建时，在构建进行到一半时遇到了这个奇怪的错误。

```
(libuv) kqueue(): Too many open files in system
net.js:271
    err = this._handle.open(fd);
                       ^

Error: ENFILE: file table overflow, uv_pipe_open
    at new Socket (net.js:271:24)
    at createWritableStdioStream (internal/process/stdio.js:191:18)
    at process.getStdout [as stdout] (internal/process/stdio.js:20:14)
    at console.js:469:19
    at NativeModule.compile (internal/bootstrap/loaders.js:339:7)
    at Function.NativeModule.require (internal/bootstrap/loaders.js:200:18)
    at setupGlobalConsole (internal/bootstrap/node.js:444:41)
    at startup (internal/bootstrap/node.js:132:7)
    at bootstrapNodeJSCore (internal/bootstrap/node.js:826:3)
error Command failed with exit code 1. 
```

我从没见过这个。文件表溢出？！！？听起来很有趣。我决定四处挖掘，弄清楚可能发生了什么事。一些谷歌搜索显示，这是因为我的系统上有太多打开的文件。不过这似乎非常可疑。我已经运行这个命令几百次了，没有一次失败。为什么是现在？

我做了一些谷歌搜索，以找出我的系统上打开的文件描述符的最大数量，并得到以下结果。

顺便说一下，对于那些可能不熟悉的人来说，文件描述符是用来唯一地标识我们操作系统上打开的文件的。尽管名称中包含单词“file”，但它们也用于唯一地标识任何类似文件的对象，在 Unix 世界中包括硬件设备之类的东西。如果你对此感到好奇，我认为这篇文章做得很好。

```
$ launchctl limit maxfiles
        maxfiles    256            unlimited 
```

`256`表示可以打开的文件的软限制，这可能是我达到的限制。`unlimited`是硬极限(绝对不能超越的极限)。

我决定再次运行 build 命令，看看是否会遇到同样的错误。这一次，我跟踪了 build 命令的进程 ID，并使用`lsof`命令来获取与该进程相关联的打开文件描述符的列表。

```
$ lsof -c node 
```

这最终成为一个相当嘈杂的命令，因为`lsof`决定打印出我机器上所有运行 Node 进程的所有文件描述符。对我来说，这包括像我的终端模拟器 [Hyper](https://hyper.is/) 这样的东西。

无论如何，我第二次运行该命令时，构建成功完成，没有任何错误从操作系统中冒出来。也许构建只是为了转移注意力，我的系统上运行的其他东西打开了太多的文件描述符？可能是我的版本中有一个依赖项导致了这个问题？也许依赖关系中的 bug 是非常短暂的，这就是为什么我只在那种情况下看到它？

关于这个问题的几篇在线文章跨越不同的软件包，建议增加系统上打开的文件描述符的最大数量限制。我决定不这样做，因为这个问题似乎更像是一个侥幸。

无论如何，这篇博文没有真正的结论或寓意。我只是在做一些工作，注意到一些有趣的事情，并决定在我工作的过程中注释我的过程。如果再碰到这个问题，我会再发一篇后续博文。

我该去睡觉了。晚安，各位开发者！