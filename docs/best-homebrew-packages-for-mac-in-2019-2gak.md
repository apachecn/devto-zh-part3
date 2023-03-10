# 2019 年 Mac 最佳自制软件包

> 原文：<https://dev.to/costapiy/best-homebrew-packages-for-mac-in-2019-2gak>

如果你是 Mac 超级用户，并且可能是软件工程师，你会在你的机器上安装自制软件。如果你不知道什么是家酿，你可以在这里找到更多信息，你可以在这里阅读如何安装它。基本上，家酿是 Mac OS X 缺失的软件包管理器，可以用来安装额外的 Unix 软件包。是一个工具，可以真正帮助你安装几乎任何东西，它可以管理依赖关系和更新软件包只需一个命令。

## 因此，这里有 10 个最好的 Mac 自制软件包

### 1。Wget

Wget 是一个命令行工具，可以通过 HTTP、HTTPS、FTP 和 FTPS 下载文件。是一个非命令行交互工具，可以在 bash 脚本、cron 作业和终端中使用。

### 如何安装:

`brew install wget`

### 2。Htop

Htop 是最好的选择，它可以通过终端提供关于 CPU 使用、可用内存、系统正常运行时间、正在运行的服务以及终止正在运行的进程的能力的附加信息。
[![htop](img/bd62a641bd8cf7526a3b0ad5a6d7eb48.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--iVhlzHWW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AEu0K89v8LyAyvN-h.png)

您可以从官方网站下载并安装它，但从家酿软件中下载更容易，只需在您的终端中键入:

`brew install htop`安装完成后输入`htop`即可使用。

### 3。Youtube-dl

dl 是一个命令行程序，可以让你下载 Youtube 视频和声音。它也可以从其他几个网站下载内容。输入
`youtube-dl -f best 'youtube-link'`即可使用

这将使您能够下载 YouTube 拥有的最佳视频质量。虽然，is against YouTube 的条款和条件是一个非常有用的程序，可以帮助你下载你已经上传到 YouTube 的视频，但你的本地机器上已经没有这些视频了。可以用 Homebrew 下载 youtube-dl 如下图:
`brew install youtube-dl`

### 4\. Ripgrep

Ripgrep 是一个命令行工具，可以递归搜索您的当前目录。它可以和正则表达式模式一起使用，也可以遵守 gitignore 规则。Ripgrep 适用于 Mac OS X、Linux 和 Windows。你可以在这里下载二进制文件，也可以用自制软件下载，如下所示。

`brew install ripgrep`

### 5 个。Imagemagick 了

Imagemagisk 有许多功能，可以将图像从一种格式转换为另一种格式，它还可以进行一些基本的图像编辑，如翻转、旋转、镜像等。Imagemagisk 的创作者也考虑到了性能。Imagemagisk 利用多线程，因此可以非常快速地执行多个 I/O 操作。要安装它，您可以:

`brew install imagemagisk`

丰富的 API 文档和 Imagemagisk 的全部功能可以在这里找到。

### 6。精力

Vim 是您可以使用的著名的、高度可配置的、有点争议的文本编辑器。它有各种各样的插件，在我看来，如果你花一些时间来适当地配置它，并学习所有的命令，你会比使用一个带 UI 的文本编辑器写得快得多。它支持数百种编程语言，并且出现在每个 UNIX 系统中。然而，如果你想轻松获得最新版本，你应该通过自制软件安装。一个非常好的网站会帮助你学习 VIM，它叫做 [VIM Adventures。](https://vim-adventures.com/)您将通过玩游戏来学习基本命令，这是一种学习工具的有趣方式。

`brew install vim`

对于苹果电脑来说，有一个很好的如何正确配置的指南。可以在这里找到[。](//sourabhbajaj.com/mac-setup/Vim/README.html)

### 7。Zsh

Z shell 是另一种 shell，例如 bash(MAC 上的默认设置)。Zsh 提供了额外的功能，当它与一个框架一起使用时，它会非常强大，可以帮助您提高工作效率。Zsh 是 Mac OS X 自带的，但是已经过时了，而且通常情况下，你不会得到频繁的更新。这就是为什么通过 brew 安装更好。

`brew install zsh`

我推荐和 Zsh 一起使用的框架叫做[我的 Zsh。我将在以后的帖子中展示我的 Zsh + iTerm2 设置。Zsh 有非常好的插件和漂亮的主题。我总是发现自己每隔几个月就在寻找好的主题，只是为了改变我的终端的外观。](https://github.com/robbyrussell/oh-my-zsh)

### 8\. Unrar

每个人都知道 unrar。这是著名的解包器的命令行版本。它完全支持 RAR 和压缩文件。它可以解压的格式有 GZIP，ACE，CAB 等等。安装:

`brew install unrar`

### 9。平行的

Parallel 是一个命令行应用程序，可以在您的本地环境中或远程同时执行多个作业。一个典型的用例是拥有一个您想要多次运行的脚本。它可以接受多个主机、用户、URL 或文件作为输入。您可以通过执行以下命令来安装它:

`brew install parallel`

你可以查看下面的视频来了解如何使用它。
[![GNU Parallel](img/7932b4881d4e05270ea08d98c1ac6dc0.png)T3】](https://youtu.be/OpaiGYxkSuQ)

### 10。Speedtest-cli

顾名思义，这是一个对你的网络连接进行速度测试的工具。基本上是 Speedtest.net 网站的命令行版本。

`brew install speedtest-cli`

这就是所有人，如果你有任何其他有用的自制工具，你想在这里看到，请直接联系我。如果你觉得有帮助，别忘了分享这篇文章，也别忘了订阅[。](https://picocoder.io/#subscribe)

最初发表于 2018 年 12 月 8 日 [picocoder.io](https://picocoder.io) 。