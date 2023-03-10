# 老式计算机修复，第一部分

> 原文：<https://dev.to/rickcarlino/vintage-computer-restoration-part-i-4ndf>

<center>
![](img/1066e50dab037758915c2af80ab0d6ec.png)
</center>

去年年底，我参加了中西部老式电脑节。我花了 80 美元买了一个运行 Windows 3.1 的基于 [386 的](https://en.wikipedia.org/wiki/Intel_80386)工作站。接下来的博客文章将记录我把它放回互联网的尝试。

Windows 3.1 在我心中有着特殊的地位。这是我的第一个操作系统。作为一个 90 年代中期的孩子，这也是我第一次上网(通过 Compuserve 和后来的 AOL)。

从文件系统的日期来看，这台计算机是从 1994 年运行到 1997 年的。

## 规格和硬件

*   Intel i386 @ 25 Mhz
*   4 兆字节的 [SIPP 内存](https://en.wikipedia.org/wiki/SIPP_memory)
*   120 MB 硬盘
*   VGA 显示器配有巨大的显卡
*   串行鼠标(早于 [PS/2](https://en.wikipedia.org/wiki/PS/2_port) )
*   [在键盘上](https://en.wikipedia.org/wiki/IBM_PC_keyboard)
*   [3.5 英寸软驱](https://en.wikipedia.org/wiki/Floppy_disk)

设备受到约束。在 2019 年，甚至一些微控制器能够超越这台机器。

[![](img/01d28e544f542838c5115ab943942d38.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0NYiJoly--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rickcarlino.cimg/2019/386/specs.png)

# 项目目标

我对这个项目的意图是“让 386 做事情”，并以一种帮助其他逆向计算爱好者的方式记录我的进展。以下是我希望完成的一些事情:

**目标 1(完成):**通过任何方式用我的(现代)笔记本电脑传输文件。这可能意味着通过 USB 软驱，或串行电缆。

**目标 2(完成):**让它与面向公众的网络对话。

**目标 3(进行中):**上网并浏览[基于文本的网页](http://rickcarlino.com/2018/07/11/fabulous-text-only-websites-2018-edition-html.html)。

延伸目标:在上面运行一个面向公众的服务器，以获得喜剧效果。HTTP 将会很有趣，尽管如果任务过于雄心勃勃，我可能会满足于一个更容易的目标(比如 telnet)。

<center>
![Cleaning the case and reseting the BIOS with Michael Christenson](img/201de064367e56932aef18311a26ab75.png)
</center>

# 为什么？

为什么不呢？

# 数据传输尝试一:Sneakernet

<center>
![](img/23f2d43b8bc2dca03328c5ca47a64ce1.png)
</center>

尽管像纸牌和扫雷这样的 Windows 3.1 经典令人兴奋，但我无法在 Windows 3.1 的全新安装上完成太多任务。我需要在上面安装软件。我有一个 USB 软驱和一箱从当地旧货店找到的软盘。

这似乎是取得进步的最简单的方法。我可以从[互联网档案软件集合](https://archive.org/details/software)下载经典的 Windows 软件，通过 USB 软盘驱动器(在我的笔记本电脑上)将它们存储到磁盘上，然后手动加载到 Windows 3.1 机器上。

然而，sneakernet 方法带来了挑战:

*   许多游戏需要超过 1.44 MB 的存储空间，而且这台机器还没有光盘驱动器。
*   我的软盘收藏有数据完整性问题。有些文件似乎可以很好地写入磁盘，但之后就不可读了。
*   2019 年 Linux 对 USB 软驱的支持不是很大。我会碰到奇怪的问题，比如驱动器拒绝安装，无休止地旋转光盘，以及每当连接驱动器时笨拙的系统关闭。这种错误在没有警告的情况下偶尔发生。

我可以加载一些著名的游戏(比如 [SkiFree](https://classicreload.com/win3x-skifree.html) ),但是这被证明是不方便的。虽然我本可以在笔记本上拆分文件，然后通过`pkzip`或类似的工具在主机上重新组合，但我决定寻找更方便的文件传输方法。

# 数据传输尝试二:串行线传输

在家庭计算糟糕的旧时代，那时广域网还不普遍，标签可以自由漫游，用户可以通过 RS-232 T2 线在计算机之间传输数据。简单的传输协议，如 [XMODEM](https://en.wikipedia.org/wiki/XMODEM) 使这种传输成为可能。这个过程就像用一根串行电缆把两台计算机连在一起一样简单。尽管很简单，协议和传输层却慢得令人难以置信。

<center>
![](img/548b1a383aba495b6bf1e92b50b38084.png)
</center>

要解决的第一个问题是购买 RS-232 转 USB 适配器，因为我的笔记本电脑(以及本世纪售出的几乎每台电脑)都没有 RS-232 端口。

在亚马逊上购买了一个适配器后，我才知道很多卖的型号都需要一个公母 USB 延长线，需要网购第二套。

另一个不便是机器只有一个串行端口。这意味着我可以使用鼠标或进行数据传输，但不能同时进行。由于串行鼠标在 Windows 3.1 上不支持热插拔，从“鼠标模式”切换到“文件传输模式”需要完全重启我的机器。为了不被吓倒，我花了一些时间重新学习 Windows 3.1 键盘快捷键，并准备好为数据传输配置一个软件工具链。

**文件传输咒语**

现在我已经有了所有需要的电缆和适配器，我准备配置软件。我使用的主要工具是:

*   GNU `screen`:在 Linux 端打开一个串口。
*   `sx`:“发送 XModem”。用于 XModem 传输的*nix 工具
*   `terminal.exe`:用于在 Windows 3.1 上打开串口([截图](https://guidebookgallery.org/screenshots/win31))。

为了打开串行端口并开始串行传输，我执行了以下咒语:

1.  `screen /dev/ttyUSB0 9600`
2.  类型`ctrl + a`
3.  类型`:`
4.  输入`sx -b FILE_RELATIVE_PATH_GOES_HERE`
5.  在 Windows 3.1 中点击`Transfers => Begin Binary Tranfer``terminal.exe`

这很快变成了一项乏味的任务。我最终[编写了一个定制的 Ruby 脚本来自动化这个过程](https://github.com/RickCarlino/Dialup-Modem-Tools#x-modem)并将步骤从 5 个减少到 1 个。

# 接下来是什么？

我的 Windows 3.1 机器运行良好，正在将文件从互联网存档转移到磁盘。

在我的下一篇文章中，我将谈论为有趣的事情打开 TCP 套接字，比如查看[维基百科 Telnet 网关](https://meta.wikimedia.org/wiki/Telnet_gateway)以及我在 Internet Explorer 5 上获得 HTTP 访问的持续斗争。

如果你是这个时代的电脑用户，并希望与我分享建议，请通过 Reddit 或 Lobste.rs 联系我。