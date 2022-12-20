# 如何学习渗透测试:初学者教程

> 原文：<https://dev.to/ctrlshifti/how-to-learn-penetration-testing-a-beginners-tutorial-505a>

免责声明:黑客是一项很难学习的技能。仅仅上几门在线课程不会让你成为一名优秀的 pentester。仅仅通过安装 Kali Linux 和学习如何使用工具，你不会成为一个好的 pentester。这是一条充满挑战的道路，充满了无尽的挫折，你不会在一天内——一个月内——甚至一年内学会如何走好这条路。然而，如果你下定决心，你会发现 pentesting 是一个非常值得的领域，你可能永远都不想离开。

*在本教程中，我将专门介绍* ***web 应用黑客*** *方面的渗透测试。这是一个道德黑客指南。如果你在做不道德的事，我真的希望你不要。或者至少给我一部分利润。*

*假定知识:*

*   *基础技术背景(Unix 命令，一些软件开发技能)*
*   *强烈的砸东西欲望*

#### 0 -背景知识

一些 CS101 知识是必须的。试图在甚至不熟悉 Unix 命令的情况下学习如何进行黑客攻击，这不仅仅是在学会走路之前就试图跑。这就像驾驶一架 A380 飞机，却不知道哪个方向是向上的。

[![communication skills](img/474aef20f3452b171023918904b9931e.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--HcUFaHmq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.explainhownow.com/asseimg/pentest_pilot.png)

如果你试图在没有必要的先验知识的情况下投入到测试中，并在进行过程中“填补空白”,你将会陷入不必要的挣扎。以下是你需要知道的:

**如何使用 Linux**:Linux/Unix 进行编码和测试的主要动力来自终端和大量可用的工具。你可以*尝试*在 Windows 中做你需要的一切，但这并不容易——如果你正在进行测试，你最终将需要了解一些 Linux。相信我:如果你得到了一份安全方面的工作，而你的同事发现你从未使用过 Linux，他们会永远嘲笑你的。

这里有三个主要选项:

*   安装一个 Linux 发行版(比如 Ubuntu)。你最好的选择是下载一个虚拟机软件，其中可以包含你的 Linux 安装(下面的链接)。
*   继续用 macOS，如果有的话。你可以用这个来凑合，因为 MAC 上的终端和工具与 Linux 非常相似。
*   在 Windows 10 上使用 [Ubuntu。我认为这对于初学者来说是最糟糕的选择，因为在安装工具时，它可能相当不可靠，而且让 GUI 工作有时可能是一场噩梦。](https://tutorials.ubuntu.com/tutorial/tutorial-ubuntu-on-windows)

*   [VirtualBox](https://www.virtualbox.org/) :免费虚拟机软件

*   Ubuntu :体面的 Linux 启动发行版

*   [在 VirtualBox 中安装 Ubuntu](https://itsfoss.com/install-linux-in-virtualbox/)

*   [适用于初学者的 Unix 命令](http://www.ee.surrey.ac.uk/Teaching/Unix/)

**如何编码**:现在你已经设置好了你的环境，我们可以开始有趣的部分了！学习一些基本的编码技能对 pentesting 是必不可少的。想学怎么破，先学怎么造。对于 web 应用程序测试，您将需要学习一些完整的东西，比如 HTML、CSS、Javascript 和 Python。Python 还有一个额外的好处，那就是它是一种优秀的脚本语言，并且允许你编写自己的测试工具(令人兴奋！).

*   [学习前端](https://medium.com/applantic/getting-started-with-front-end-development-in-2018-6ce4993926d8)
*   [学习 Python](https://docs.python-guide.org/intro/learning/)

#### 1 -设置您的环境

如果你是一个开发者，你可能已经有了完美的设置。格拉茨。这里走的路一般是 Linux 或者 Mac。就我个人而言，我在 Windows 10 上使用 Ubuntu(告我吧)，但这只是因为我知道所有我喜欢的工具都可以在上面工作。

很多初学者从卡利开始，但我建议不要这样。成为自信的 pentester 的一部分是建立你的工具库。Kali 给了你一堆工具，没有一个是你真正理解和欣赏的。

但是无论你在做什么，拥有一个舒适的环境是绝对重要的。现在花些时间来解决你在设置中可能遇到的任何问题(比如引导加载程序、窗口管理器、GUI 等)。当你打开无数的窗口和复杂的工具时，测试会变得很混乱，你最不需要的就是你自己的环境与你作对。
T3】

#### 2 -学习理论

这一次没办法了。即使仅仅是网络应用程序黑客，你也需要了解大量的知识。我将网络黑客知识分为两类:基础知识和绝妙技巧。基础是你应该首先从书籍、视频、在线教程等学习的内容。

不幸的是，考虑到黑客世界的发展速度，大多数有能力的网站都已经安全地抵御了基本攻击(但是你仍然需要了解它们！).巧妙的技巧才是真正的赚钱机器。稍后，您将通过浏览经验丰富的 pentesters 博客、加入道德黑客社区和模糊的 Youtube 视频来了解这些。如果你是第一个发现一个俏皮把戏的人，你就能在名人堂中占有一席之地，或许还能获得很多钱。

以下是一些很好的基础资源:

*   [网络应用黑客手册](https://www.amazon.com.au/Web-Application-Hackers-Handbook-Exploiting/dp/1118026470):这是一个很好的起点。这几乎涵盖了你需要的所有基础知识。但是不要为随书附赠的“实验室”费心。
*   [OWASP 的测试指南](https://www.owasp.oimg/1/19/OTGv4.pdf) : OWASP 是 web 应用程序黑客攻击的关键玩家，这个指南*是巨大的*。它有很多你需要知道的东西。
*   Youtube 上的 live overflow:这家伙很棒——他涵盖了很多基础知识，也有很多巧妙的技巧。
*   [黑客攻击](https://www.hacksplaining.com/lessons):关于不同漏洞的大量信息。
*   SecHub :汇集了一堆不同的漏洞，还有文字报道！超级酷。
*   [学习 HTTP TCP/IP 模型](https://www.geeksforgeeks.org/computer-network-tcpip-model/)、[基本组网](https://commotionwireless.net/docs/cck/networking/learn-networking-basics/)、[数据包](https://www.ntu.edu.sg/home/ehchua/programming/webprogramming/HTTP_Basics.html)。你会需要这个的，相信我。

一旦你学会并练习了基础知识(下一节将详细介绍如何练习)，你就可以继续学习一些漂亮的技巧了。一些资源:

*   [DEF CON 视频很棒](https://www.youtube.com/watch?v=-FAjxUOKbdI)。
*   漏洞文章:有很多地方可以找到漏洞，Medium 可能是一个不错的地方。也来看看 [r/Netsec](https://www.reddit.com/r/netsec) 吧。另外，在谷歌上搜索你想了解更多信息的漏洞，并附上“书面报告”或“概念证明”，例如“XSS 书面报告”。你会发现一些非常聪明的人发来的帖子，讲述他们发现的利用东西的新方法。
*   寻找 pentesting 社区并加入他们。令人惊讶的是，黑客是一个非常社会化的领域，仅仅通过与其他 pentesters 交谈就可以学到很多很酷的技巧。

#### 3 -用反恐部队和战争游戏练习

这是有趣的部分。一旦你有了一些理论，你就可以开始通过黑客挑战来练习。这些是带有隐藏“标志”的易受攻击的 web 应用程序，您可以通过利用该应用程序找到它们。

CTF(夺旗)比赛是现场比赛，有记分牌和团队，而战争游戏则没有那么激烈，更像是练习技能的操场。

查看当前和即将推出的 CTF，尽管大多数对初学者来说太难了。好的战争游戏是 [OWASP 的 WebGoat](https://www.owasp.org/index.php/Category:OWASP_WebGoat_Project) 和 [OverTheWire](http://overthewire.org/wargames/natas/natas0.html) 。还可以看看 [OWASP 的果汁店](https://www.owasp.org/index.php/OWASP_Juice_Shop_Project)、 [Hacker101 CTF](https://ctf.hacker101.com/) 、[黑盒子](https://www.hackthebox.eu/)、[谷歌的 XSS 游戏](https://xss-game.appspot.com/)。

虽然这很有趣，也是一种很好的学习方式，但请注意，你在战争游戏/CTF 中需要的技能与你在现实生活中需要的技能有些不同，比如臭虫奖励。有可能成为 CTFs 的最佳射手，但是完全没有能力获得 bug 奖金(有一段时间我就是这样)，反之亦然。

战争游戏之于 bug 赏金就像文明 5 之于治理一个真实的国家一样。战争游戏教你一些优秀的策略和解谜技巧，但是现实生活是一幅不同的风景——在第五部分会有更多的介绍。

#### 4 -擅长编写脚本

这会让你的生活变得更加轻松。Python 作为一种脚本语言令人惊叹，尤其是对于黑客来说。许多 CTF 和 bug 奖金将需要强力操作，例如发送许多数据包和散列，所有这些都可以通过编写自己的 Python 脚本轻松完成。

看看 Python CTF 框架 [pwntools](https://github.com/Gallopsled/pwntools) 。它简化了漏洞利用的编写！[这里是](http://docs.pwntools.com/en/stable/tubes/sockets.html)你如何发送数据包。

我建议创建一个文件夹，保存您自己的 Python 脚本，并随着时间的推移在其上进行构建。我真的不能轻描淡写这将节省你多少时间。

#### 5 -真实世界和臭虫奖励

在某个时候，你会得到你的第一个中等难度的 CTF 挑战的标志，而不必谷歌解决方案。你会感觉很棒。很可能，你已经在这上面花了很多时间，最后你自己找到了答案，这种感觉会让你永远迷上 pentesting。

你现在是猎人了。凶猛。不可阻挡。

[![communication skills](img/7028717a1cadc66c685b665feb7d56ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zIKstwU_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.explainhownow.com/asseimg/pentest_hunter.png)

你甚至可能认为你已经准备好开始赚钱了。但是一旦你查看了 bug bounty 网站，你就会意识到你根本不知道自己在做什么。没有任何线索能告诉你漏洞在哪里。攻击面如此之广，你甚至不知道从哪里下手。成千上万更优秀的黑客已经把网站清理干净了。

尽管可能令人沮丧，但这才是乐趣真正开始的地方。你已经走出操场，准备好和大孩子一起玩了。一个很好的起点是观看[我之前链接的这个 DEF CON 视频](https://www.youtube.com/watch?v=-FAjxUOKbdI)，并深入寻找好的工具和更多漂亮的技巧。

现在是开始学习网络侦察的时候了。这在 DEF CON 视频中有很好的介绍，当你建立侦察工具库时，你会学到更多。

#### 6 -了解你的工具

工具造就不了黑客。但是没有它们你可能走不了多远。

我建议从下载几个“强制”工具开始，比如 [Nmap](https://nmap.org/download.html) 和 [Burp Suite](https://portswigger.net/burp) 。Nmap 是一个发现工具，它可以发现主机和域上开放的端口，通常会让您对网络有一个良好的感觉。打嗝套件是你新的最好的朋友。说真的。这是网络黑客的头号多功能工具。它的主要用途是捕获和编辑数据包，但它的作用远不止于此。我真的无法在这篇博文中给它一个公道——只需谷歌一下，看一些教程视频。

那两个之后，就看你自己去找(或者做)最适合自己的工具了。以下是我最喜欢的一些:

*   [Sublist3r](https://github.com/aboul3la/Sublist3r) :我绝对爱上了这个子域枚举器。它很快就找到了一堆东西。
*   [Aquatone](https://github.com/michenriksen/aquatone) :类似于 Sublist3r，但是更加健壮。用速度换取力量；我一般先运行 Sublist3r，然后在后台保持 Aquatone。
*   [目录搜索](https://github.com/maurosoria/dirsearch):原始目录。
*   [LinkFinder](https://github.com/GerbenJavado/LinkFinder) :发现 Javascript 文件中的端点。
*   一个完整的网络侦察框架，可以做几乎所有的事情。如果你找到合适的环境，我可能会帮你遛狗，为你做饭。
*   SecLists :本身不是一个工具，而是一个列表集合，用于暴力破解。几乎是网页测试的一个主要部分——我几乎把它放在了必选部分。
*   [Spotify 黑掉混音带来爽一下](https://open.spotify.com/playlist/0Jt2JW0NTIL6MvV9dSOnqZ)

#### 7 -继续黑客攻击

我告诉过你这很难，不是吗？

Pentesting 充满挑战，令人困惑，总的来说令人沮丧。但是如果这是你真的想做的事情，你会找到克服这些困难的方法。

尝试加入社区，比如 Twitter 和 T2 的 Bugcrowd，因为和别人一起的旅程总是更有趣。

记住:这是一个真正重要的领域。这是值得的，你将为这个世界做正当的好事。黑帽黑客每天都在学习，道德黑客社区需要一切可能的帮助。祝你好运，一帆风顺！

*本帖最初发表于[explainhownow.com](https://www.explainhownow.com/)T3】*