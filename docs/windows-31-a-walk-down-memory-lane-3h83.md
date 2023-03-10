# Windows 3.1，走过记忆的小路

> 原文：<https://dev.to/huijing/windows-31-a-walk-down-memory-lane-3h83>

欢迎来到另一个版本的《惠晶做一些没有实际用途的事情，但还是要做，因为为什么不呢？在当地的说法中，我们称之为“shiok sendiri”，你可以谷歌一下。

因此，我的好朋友，也是复古技术的爱好者，Kheng Meng，帮了我一个大忙，同意了我这个轻率的谈话计划。CSS #39。这个想法是在他的 IBM ThinkPad 390 上运行 Windows 3.1 的原始版本，并加载 Internet Explorer 3。为什么是 IE3？因为它是[第一个支持 CSS 的商业浏览器](https://www.w3.org/Style/CSS/msie/)。

轻率的计划如下:Kheng Meng 提供硬件并承担系统管理员的责任，而我构建一个加载和显示良好(以及 IE3 可以达到的)的网站，该网站针对现代浏览器进行了逐步增强。

[![Where I say the words IE5 is too advanced](img/5de3caf81a4fc82d3e1b6f110b988826.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gk-s5tCZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/win31/chat.jpg)

额外的问题是，直到那天之前，我不会在机器上进行测试。因此，这要么是壮观的，要么是壮观的失败。我倾向于后者，但不管怎样，都很有趣。如果你在 2019 年 5 月 22 日在新加坡，来现场看看这个荒谬的想法。关于 meetup.com 的细节。

## 怀旧开始了

我认为，作为一个高效率的人，Kheng Meng 在一周内就把一切都准备好了。他勇敢地把大约 7 公斤重的硬件拖到[黑客聚会](https://www.meetup.com/Hackware/)(在那里他讲述了他获得飞行员执照的经历，[视频在这里](https://youtu.be/GtT5wCYhZBA))让我玩！

[![Proof that IE3 version 3.03 SP1 was installed](img/5dd6a81f161323269bad5d32df51041a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fRm-bfeo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/win31/proof-1280.jpg)

他已经安装了 IE3、 [Netscape Navigator](https://winworldpc.com/product/netscape-navigator/40x) 和 Opera，但也包括一些有趣的东西，如 [SkiFree](https://ski.ihoc.net/) 。如果你不知道 SkiFree 是什么，那太糟糕了，你错过了几个小时的乐趣。这立刻引发了很多童年的回忆，说实话大多是游戏。

最终，我决定通过 VirtualBox 运行我自己的 Windows 3.1，这样我就可以访问最初的 Windows 娱乐包游戏。同样，如果你不知道这些是什么，你就错过了。还好那时 FOMO 还不是个东西，对吧？

[![SkiFree on my virtual Windows 3.1](img/7a1d1182634b6bc1587653fe0cf6f3af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JHpCFTe0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/win31/skifree-1280.jpg)

## VirtualBox 上的 Windows 3.1

我找到了 2 个可靠的教程，涵盖了在 VirtualBox 上安装和运行 Windows 3.1 的端到端指导。一个是来自澳大利亚巴拉拉特的乔希，他经营着一个很棒的博客 [Socket 3](https://socket3.wordpress.com/) ，充满了复古的美好。

这篇文章是[安装&使用 Oracle VirtualBox](https://socket3.wordpress.com/2016/08/25/install-configure-ms-dos-6-22-and-windows-3-1-using-oracle-virtualbox/) 配置 MS-DOS 6.22 & Windows 3.1，除了联网之外，我基本上没有遇到任何问题。经过一些谷歌搜索，我认为这可能是因为我的主机名称太长了。

显然，电脑名称必须少于 8 个字符，但也许是 15 个及以下？我不知道。所以尽管一切都安排好了，我的游戏也安装好了。我销毁了它，并再次尝试使用 Windows 3.1 for Workgroups，而不是第二次。

*注意，到磁盘映像的链接将在最后提供*

按照所有说明进行操作，直到联网选项启动。尽可能遵循原始教程，如果你使用包含的*AMD PCNET Network drivers . img*文件，注意驱动程序的位置在 *A:\WFW31*

在安装软盘的一些虚拟交换之后，事情应该开始运行了。尽管如果你遇到各种 X.EXT 文件找不到的问题也不用担心，因为我确实遇到过。

[![The classic Windows 3.1 Program Manager](img/7d020a4a7c930e117d2c8c162f6e84b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZrBLxThr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/win31/desktop-1280.jpg)

一旦 Windows 安装完毕并被证明运行正常，你就可以添加教程中概述的有趣的东西，包括一个更好的图形驱动程序(这使得屏幕更大)，以及广受欢迎的 TADA 启动调。

对于 CD-ROM 驱动程序，我按照标题为[的](http://blog.chaoscontrol.org/install-ms-dos-6-22-in-a-virtual-machine-for-fun-and-profit/) [Chris Sprague](https://twitter.com/_roguerobot) 的说明将 MS-DOS 6.22 安装在一个虚拟机中，以获得乐趣和利益。从他的链接中抓取驱动程序，像你目前使用的其他安装盘一样加载它。

## Windows 娱乐包

这是我最喜欢的事情，当注意力持续时间超过 5 分钟的时候。反正我玩俄罗斯方块玩到某个时候闭上眼睛就能看到积木掉下来。

但是其他的一些游戏也很浪费时间，如果你已经启动并运行了，一定要试试《老鼠赛跑》和《啮齿动物的复仇》。你会惊讶于它们有多容易上瘾。事实上，Windows Entertainment Pack 的游戏套件占据了我童年的大部分时间。

[![RattlerRace and Rodent's Revenge on my virtual Windows 3.1](img/3fedd6d7e7035955d50f8828687fa127.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qSqq-A_f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/asseimg/posts/win31/games-1280.jpg)

如果你安装了第二个 Windows Entertainment Pack，你也会得到这个最终成为微软麻将游戏的早期版本，它很可能是基于 1981 年的经典游戏 Brodie Lockhart 的麻将。

此外，SkiFree 在所有它的 pixeley 荣耀。我不得不把这个拷贝到一个空白的软盘映像上，然后从 A:安装它，因为我们过去不都是这样安装游戏的吗？现在，如果我能记得我以前玩的高尔夫球游戏叫什么…

## 链接

这些是我使用过的适合我的安装源。不幸的是，如果有些东西不适合你，你可能不得不在谷歌上搜索备用安装软盘映像。希望不会到那一步。

*   [WinWorld](https://winworldpc.com/library/operating-systems) 用于操作系统、娱乐包的链接
*   [ET4000 SVGA 视频驱动程序](http://localhost:4321/assets/files/svga-drivers.zip)，从 Josh 的网站获得
*   从 Josh 的网站获得的 SoundBlaster 16 声音驱动程序
*   [CD-ROM 驱动程序](http://localhost:4321/assets/files/cdrom-driver.img)，从 Chris 的网站获得