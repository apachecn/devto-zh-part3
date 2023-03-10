# 我的开发者起源故事

> 原文：<https://dev.to/lefebvre/my-developer-origin-story-2m6i>

我从十几岁就开始写软件，从在 [Atari 800XL](https://en.wikipedia.org/wiki/Atari_8-bit_family#Newer_XL_machines) 上的 [Atari BASIC](https://en.wikipedia.org/wiki/Atari_BASIC) 开始。在大学里，我主要用的是 [Turbo Pascal](https://en.wikipedia.org/wiki/Turbo_Pascal) 和 C。而且还使用 Lisp、Fortran、Scheme 和其他语言。在 90 年代初，我为 Atari ST 编写了几个应用程序(使用 Pascal、C 和 GFA BASIC)，其中一个是共享软件，赚了不少钱，至少对一个穷大学生来说是这样。

我离开学校后的第一份工作是使用一个基于 DOS 的工具，名为[高级启示](https://www.revelation.com/index.php/products/advanced-revelation)。这是一个构建数据库应用程序的集成系统(它使用自己的专有数据库)。它的编程语言叫做 R/BASIC。当时，我们所有的工作站都装有面向工作组的 Windows 3.1，我认为这是一个糟糕的操作系统。所以我得到许可，将我的电脑切换到 [IBM OS/2 Warp](https://en.wikipedia.org/wiki/OS/2#1994%E2%80%931996:_The_) 进行我的所有开发。OS/2 曲速很流畅。这是一个现代的操作系统，也可以运行 DOS 和 Windows 应用程序，通常比 Windows 本身更好。

在开发这个应用程序几年后，我被安排负责将它移植到微软视窗系统的项目。这包括使用 PowerBuilder 重写整个应用程序，power builder 是另一个非常适合数据库的集成环境。Oracle 被选为数据库服务器，因此我还必须编写一个数据库转换工具，以便将数据从 Advanced Revelation 专有数据库中取出并放入 Oracle 数据库。与我以前用过的所有编程系统不同，PowerBuilder 是面向对象的，我很快就喜欢上了它。

不幸的是，PowerBuilder 是一个 32 位应用程序，不能与 OS/2(只能运行 16 位 Windows 应用程序)一起工作，所以这意味着我必须切换到 Windows。然而，我不打算使用 Windows 95，而是跳到了 Windows NT 4.0，这至少是一个现代的操作系统，即使它是 Windows。

你会注意到我从未用过麦金塔电脑。用过雅达利 ST 电脑，总觉得麦金塔不如。我也不喜欢最初的 Mac OS，在我看来，它没有抢占式多任务处理功能，比 Windows 95 好不了多少。但在 2001 年初，我的一个研究生朋友带着他的新玩具来了:一台 PowerBook。我并不太感兴趣，但他给我看了苹果新的麦金塔操作系统的测试版， [Mac OS X](https://en.wikipedia.org/wiki/MacOS) 。这让我印象深刻。Mac OS X 是一个成熟的 Unix 系统，有着漂亮的用户界面和现代的操作系统功能，比如抢占式多任务处理。我立刻跑出去买了一本关于 OS X 的书，这样我就可以了解更多。

我对此印象深刻，当苹果公司在 2001 年 5 月宣布他们将发售所有预装 OS X 的新 Mac 电脑时，我订购了一台 PowerMac G4。我曾经把它引导到 Mac OS 9，那只是因为它仍然是默认的 OS。我把默认操作系统换成了 Mac OS X，再也没有回头。我记得当时人们抱怨 Mac OS X 有多慢，但我可以告诉你，它不比使用 Windows NT 慢！

在拥有 PowerMac 几个月后，我开始渴望为它编程。我在日常工作中仍然主要使用 PowerBuilder(也有一点 Java ),并开始寻找为 Mac 编程的选项。

编程选择很少。CodeWarrior(顺便说一句，这是一个很酷的 IDE 名字)很常用，但是我对 c 语言编程真的不感兴趣。Java 可以在 Mac OS X 上运行，但是当时没有好的 IDE 可用。然后我不知何故偶然发现了一个叫做 [REALbasic](http://www.xojo.com) 的东西，据称是 Visual Basic 的克隆版。

我从未使用过 Visual Basic，听到的大多是关于它的坏消息。但我下载了 REALbasic 试用版并试用了一下。我认为它有潜力，所以我买了马特·纽伯格的书《REALbasic:权威指南》,花了几个月的时间研究它。根据 Matt 的优秀书籍，我确定 REALbasic 将是一个不错的选择，因此在 2001 年 12 月下旬，我订购了 REALbasic Professional。如果我没记错的话，我订购的时候是 3.5 版本，但 4.0 版本在我订购后几周才出来，所以公司实际上推迟了给我发送手册(这是他们印刷手册的时候)，以便他们可以给我发送新的手册。

当时吸引我的是 REALbasic 的面向对象的功能，这与 PowerBuilder 的功能相当，以及它的 UI 设计器。我还被一个事实所吸引，即 REALbasic 也有自己的原生数据库引擎。IDE 对我来说有点奇怪，它来自 Windows 背景，到处都是 Mac 风格的窗口，但我很快就习惯了。

起初我并没有使用 REALbasic 做任何有意义的事情，后来在 2002 年初，我看到了一个关于新的 REALbasic 杂志的帖子，并正在寻找专栏作家。我为杂志提议了一个数据库专栏，但是没有通过最初的筛选。该杂志在纽约 MacWorld 首次亮相后，有人告诉我，排名第一的请求是数据库专栏，所以我的专栏被添加到了第二期，从那以后，我在每期都有一个类似的专栏。

2003 年初，我成立了 LogicalVue 软件公司。在此期间，我创建了一些 REALbasic 应用程序和工具，并通过我的网站销售，如 SQLVue、RBUnit 和 UltraToolBar。

在此期间，我跳槽到了另一家公司，从 PowerBuilder 转到了微软。NET 同时使用 C#和 VB。

2005 年，REALbasic 2005 发布了全新的 IDE，我非常喜欢它。新的 IDE 让我想起了 Visual Studio，我认为它比到处都是窗口的旧 IDE 更容易使用。

说到 Visual Studio，同年晚些时候发布的还有 Visual Studio 2005。2006 年，我开始将一个项目从 Visual Studio 2003 移植到 Visual Studio 2005。这比原本应该的要困难得多。但我能看到墙上的字迹。微软在做他们最擅长的事情，把一些美好而简单的东西变得复杂得可怕。我开始对. NET 不抱幻想了。

2007 年 8 月，当我决定开始寻找 REALbasic 咨询工作时，这一切都到了紧要关头。我找到了很多。以至于到了 2008 年春天，我有足够的工作要做，以至于不得不放弃我的全职工作。这样我就可以专注于基础咨询了。

经过几年的咨询后，我去了生产 REALbasic 的公司工作。从那时起，REALbasic 变成了 Xojo，并扩展到能够创建桌面(Windows、Mac、Linux)、web 和 iOS 应用程序。我喜欢和 Xojo 一起工作，向人们展示它是一个快速开发应用程序的伟大工具。

你的开发者起源是什么？