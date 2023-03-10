# 在 Linux 中安装 Haskell 编译器

> 原文：<https://dev.to/lautarolobo/installing-haskell-compiler-in-linux-38dn>

假设你知道 Haskell 是什么(如果不知道，[阅读这篇简介](https://dev.to/lautarolobo/what-s-haskell-1om3)，并且你知道 Glasgow Haskell 编译器是什么(如果不知道，查看[这篇](https://en.wikipedia.org/wiki/Glasgow_Haskell_Compiler))，我将向你展示如何在你的计算机上启动和运行 GHC。

首先，打开终端，输入:

```
sudo apt-get install haskell-platform 
```

这个命令应该可以在最常见的 Linux 发行版上运行( [Debian](https://www.debian.org/index.en.html) 、 [Mint](https://linuxmint.com/) 、 [Ubuntu](https://www.ubuntu.com/) )。

按回车键，你将被要求输入你的密码，在你输入密码后，你将看到一个建议的软件包列表，那些不会被安装的，和一个将被安装的软件包列表，以及它们愿意在你的驱动器中占用的空间量。经典安装前信息。然后，如果您想继续安装，您必须键入 *s* ，或者如果您后悔，您必须键入 *n* 。

请记住，一旦您键入 *s* ，安装过程可能需要一些时间，这取决于您的互联网连接。

按照这个简单的操作，您已经安装了 Glasgow Haskell 编译器，可以使用了。

如果你想确保它已经安装，只需打开终端，简单地输入:

```
ghci 
```

因此，您应该会在您的控制台中看到类似这样的内容:

```
GHCi, version x.y.z: http://www.haskell.org/ghc/ :? for help
Prelude> 
```

* * *

没起作用？请在评论中告诉我，我很乐意帮助你！

如果你想写你的第一个 Haskell 函数并用 GHC 测试它，点击我！