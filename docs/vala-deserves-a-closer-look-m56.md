# 瓦拉值得近距离观察

> 原文：<https://dev.to/goober99/vala-deserves-a-closer-look-m56>

这并不是一个关于瓦拉的帖子——尽管我确实认为瓦拉值得更仔细的观察。它是关于评估编程语言的一般哲学。我还写了一篇与 Perl 无关的文章。

有如此多的编程语言，很难确定哪种值得我关注。我最近[开始寻找](https://dev.to/goober99/right-tool-for-the-job-but-chances-are-the-right-tool-is-perl-ck8#so-many-tools-to-choose-from)一门新的语言来学习，以努力继续扩展我的工具集。我有一个不断增长的编程语言列表，我希望有一天能学会。一种不在列表中的语言是瓦拉语。

我曾经认为 Vala 是 GNOME 之外的人不感兴趣的 GNOME 事物，但是最近我看到 Vala 在一些意想不到的地方出现，所以我决定仔细看看它。

我评估一种编程语言的方法之一是看用它构建了什么样的酷项目。我的做法是进入 GitHub 的[趋势页面](https://github.com/trending)，从右边的语言下拉列表中选择语言。

如果我找到的只是语言解释器/编译器、一个 web 应用程序框架和一些分类库，我通常不会在那种语言上花太多时间。我对人们用来建造酷东西的语言感兴趣。如今，每种语言(无论多么晦涩难懂)似乎都至少有一个 web 应用程序框架。本月 Crystal 的趋势页面显示了不少于四个 web 框架:Amber、Amethyst、Kemal 和 Lucky。第四次有 [1991](https://www.1-9-9-1.com/) 。尼姆有[杰斯特](https://github.com/dom96/jester)。闲聊有[海边](https://en.wikipedia.org/wiki/Seaside_(software))。

我也不太关心模块数。你的语言可能有四个不同的库来解析 JSON 和多个 ORM，但是我想知道的是人们在用这些库构建什么。如果人们正在用一种语言构建很酷的项目，我认为一个强大的生态系统和有用的库将会随之而来。

我意识到 GitHub 可能无法承载一种语言中所有很酷的东西，但我觉得它很有代表性。也许很酷的事情是用一种语言做的，但它们是专有的。如果一种语言使用起来很有趣，我认为它将被用于专有和开源项目，所以我认为开源项目足以判断一种语言。SourceForge、GitLab 等网站上也可能有很酷的项目。到目前为止，GitHub 拥有最大的开源项目集合，因此它可以很好地提供一种给定语言的可用内容。

## 关于瓦拉的部分

我想既然我的标题中有 Vala，我应该分享一些 Vala 正在建造的很酷的项目，这些项目说服我仔细看看它。

*   [彰](https://github.com/akiraux/Akira)。这是一个用于创建 UI 模型的 UI/UX 设计工具。它甚至还没有一个稳定的版本，但它已经在 GitHub 上有 1526 颗星，在 [Patreon](https://www.patreon.com/akiraux) 上承诺每月 560 美元。它还有一个非常酷的[吉祥物](https://github.com/akiraux/Akira#official-mascot)。首席开发人员还有一个用 Vala 编写的 [SQL 客户端](https://github.com/Alecaddd/sequeler)和 [FTP 客户端](https://github.com/Alecaddd/taxi)。

*   [Birdfont](https://github.com/johanmattssonm/birdfont) 。字体编辑器。许多 Linux 发行版的 repos 中都有包，OpenBSD 中也有一个包。还有 Windows 和 Mac 的二进制文件。这个项目是 Vala 不局限于 GNOME 的证据。我还觉得很有趣的是，GitHub 认为 11.3%的源代码是用 Brainfuck 编写的，因为。bf 扩展用于 Birdfont 格式的字体。

*   [偷看](https://github.com/phw/peek)。GIF 屏幕记录器。为许多发行版打包。可能对创建 READMEs 的动画截图有用。

*   [pdfpc](https://github.com/pdfpc/pdfpc) 。演示者控制台支持 PDF 文件的多显示器。Vala 一定很容易学，或者有很多开发者已经知道了，因为其中一些项目能够吸引很多贡献者。这个项目有 43 个贡献者，最近一次提交是在 19 天前。

*   [时移](https://github.com/teejee2008/timeshift)。Linux 系统恢复工具。这是另一个 Vala 项目，有一个小而活跃的社区。它有 30 个贡献者和用户提交问题和拉请求。

*   [期末](https://github.com/p-e-w/finalterm)。这是一个具有高级功能的终端模拟器。现在已经不维护了，但是曾经很受欢迎(在 GitHub 上有 3999 颗星)。

*   初级 OS 的默认 app 大部分都是用 Vala 写的。我没有用过(我不会放弃我的 MATE 桌面)，但是 elementary OS 是一个流行的 Linux 发行版(目前在 DistroWatch 上排名第四)。它的应用商店、音频播放器、计算器、日历、电子邮件客户端、文件管理器、图像查看器、面板、截图工具、终端仿真器、文本编辑器、视频播放器，甚至它的窗口管理器都是用 Vala 编写的。我质疑在已经有这么多很好的选项可供选择的情况下，还需要编写这么多自己的应用程序的必要性，但他们确实有一致的设计美学和生态系统。许多为初级操作系统编写的第三方应用程序(但可以与其他发行版一起使用)也是用 Vala 编写的(例如这个[电子书阅读器](https://github.com/babluboy/bookworm)和[专注写作应用程序](https://github.com/lainsce/quilter))。

*   Deepin 是中国流行的 Linux 发行版，有自己的桌面环境。与使用 GTK 的基本操作系统不同，它使用 Qt，但它仍然在一些事情上使用 Vala。它的[终端模拟器](https://github.com/linuxdeepin/deepin-terminal)和它的[窗口管理器](https://github.com/linuxdeepin/deepin-wm)(它最初是基本操作系统窗口管理器的一个分支)都是用 Vala 编写的。

*   有一些 GNOME 官方应用是用 Vala 写的，但令人惊讶的是，由于 Vala 是 GNOME 项目，所以不是很多。GNOME [磁盘使用分析器](https://gitlab.gnome.org/GNOME/baobab/)、[备份工具](https://gitlab.gnome.org/World/deja-dup)和[照片管理器](https://gitlab.gnome.org/GNOME/shotwell)都是用 Vala 编写的。

*   甚至还有一个用 Vala 写的 [web 应用框架](https://github.com/valum-framework/valum)。

Vala 可能没有像 Rust 一样“重写所有东西”的开发人员，也没有 Go 和 Reason 这样的科技巨头的支持。我平均每七年开发一种新的编程语言，所以这是一个重大的决定。我在慢慢来。我在考虑让 Rust 或 D 成为我的下一个语言，但现在我也在考虑 Vala。