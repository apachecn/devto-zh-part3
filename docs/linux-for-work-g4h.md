# 用于工作的 Linux

> 原文：<https://dev.to/nickhuanca/linux-for-work-g4h>

我已经在一台 Linux 笔记本电脑上工作了大约九个月，我想回顾一下我的学习成果，以造福他人。

所以在过去的九个月里，我意识到我改变的所有原因与让我继续使用 Linux 的原因并不相同。发现转换的隐性成本和隐性收益是非常有趣的。

首先，为了概述我的一些动机，我应该指出我是一名 SRE/“devo PS”工程师，管理 Linux 和容器化的分布式系统。我对 Linux 和服务器上可能发生的所有问题都很满意。也就是说，我不管理任何安装了 X Windows、蓝牙或其他工作环境“必备”的系统(比如 Spotify)。

我最初转换的原因是:

*   Mac 硬件有一些问题(我不想要 touchbar，但我想要最新的 CPU 和内存规格)
*   一个更加原生的环境，类似于我习惯在我使用的服务器上工作的环境
*   最近在安装了 Debian 的个人笔记本电脑上的体验，我真的很喜欢设置和让事情顺利进行
*   性能调优和控制(稍后将详细介绍)

我从 OS X 跳下的跨栏:

*   蓝牙可靠吗
*   Spotify 有客户吗
*   Iterm2 分割窗口和广播输入
*   VS 代码和其他 ide 或开发环境
*   我不能把我的笔记本电脑当成“[牛](http://cloudscaling.com/blog/cloud-computing/the-history-of-pets-vs-cattle/)”，它绝对是宠物
*   我会花所有的时间让操作系统正常工作吗？会让我束手无策吗？

我真的想要一些漂亮的新硬件来让我开始我漂亮的新工作。不幸的是，苹果提供的所有最好的东西都让我只能使用 USB-C，没有 mag-safe，还有一个愚蠢的 touchbar，更不用说新机型的所有键盘问题了...如果我要接受没有 mag-safe 的事实，我想我应该看看在一些非苹果硬件上工作会是怎样的。我也认为用 Linux 会很好，因为我很熟悉用它做服务器。我还和 Debian Jesse 一起运行了一段时间的旧笔记本电脑，真的很喜欢在上面玩我的开发环境。我发现大多数现代的 ide 都有一个 Linux 客户端，所以 VSCode 几乎是从 Mac 到 Linux 的 1:1 转换。Spotify 也有一个客户在移动中向我推销。在学习了将蓝牙扬声器和耳机配对的新工作流程后，我意识到还有一些其他的硬件管理器。通过在线阅读，我换了一个不同的蓝牙管理器。我学到的一件事是你可以替换任何东西，但是如果不是你所期望的，准备好收回你的决定！

至于替换 Iterm2，我发现 tmux 是一个非常好的解决方案，您可以通过点文件和其他定制来随身携带。

我改变的最初原因之一是为了更好地控制技术决策和我的笔记本电脑的性能。这最终成为一把双刃剑，但总的来说，如果你有一个减速，你真的可以学习系统知识，了解你的笔记本电脑在做什么，为什么！我最终加密了我的引导盘，并且有一个加密的主目录，这有时会很麻烦，所以永远记住强大的力量，伴随着强大的个人责任:)。

就障碍而言，我确实发现自己学到了更多关于系统如何工作以及如何维护和支持操作系统的知识。在日常“照顾和喂养”方面，我还没有发现自己因为笔记本电脑坏了而无法工作或被卡住。

我没有考虑到的缺点:

*   安装开发工具并与同事交换意见:我没有 Brew..现在怎么办..
*   只为 Darwin 构建的内部工具——这意味着你有时要构建自己的东西。
*   公司政策:大多数大型组织都在笔记本电脑上安装了代理，以便能够应用政策和控制访问。这在 Linux 或您的组织中可能是不可能的。
*   鼠标手势仍然没有 OS X 那么神奇:多点触摸可以工作，但没有真正强大的原生滑动手势功能。
*   有时候发行版的捆绑硬件管理工具并不是你最喜欢的。

老实说，我从跳转到 Linux 工作站中学到的东西让我吃惊，总的来说，我仍然会把它推荐给任何可以选择的人。在内部文档和工具之争之后，为了让我的笔记本电脑做好日常工作的准备，我花了一些时间为其他人编写问题和文档，以帮助他们进行迁移。

我仍然怀念 OS X 的一点是四个手指在工作区之间滑动，但老实说，Ctrl+Alt 向左或向右箭头在它的位置上工作得很好。

我坚持下去的理由是:

*   更多原生体验超赞
*   仅仅作为一名用户，我就增加了对社区的参与
*   在采用固态硬盘的新硬件上，启动速度快得令人难以置信

我最近真正欣赏的原因实际上大多是新的原因，并不总是我认为我想搬家的原因。将 Linux 作为日常驱动力促使我调查不一致性、报告问题并尝试新的开源工具。仅仅通过使用这项技术，我就觉得我已经有所回报了。我真的很喜欢我的系统的更新和启动性能。

建议:

*   向您的组织确认没问题！还是在家试试吧！
*   试着选择一个和你的工作相似的发行版。如果您在服务器上使用所有 CentOS，请尽量避免安装 Debian。除非你真的想两者都知道，否则只保留一个发行版会容易得多。我个人喜欢 Mint Linux，因为我们的大多数服务器都使用 Ubuntu 或 Debian。
*   您可能会忍受工作站上发生“一件奇怪的事情”,直到您弄明白:对我来说，这是我用 ZSH 和 tmux 换行。有时候它会做一些奇怪的事情，这还不足以让我真正弄明白
*   先在家试用 Linux 或者双引导。这里的想法是能够回滚到生产环境

请随意提问，但请记住，如果你正在考虑进行一次飞跃，请尽情享受和尝试！