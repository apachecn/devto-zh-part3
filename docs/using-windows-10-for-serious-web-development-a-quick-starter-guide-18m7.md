# 使用 Windows 10 进行严肃的 Web 开发——快速入门指南

> 原文：<https://dev.to/manuelbieh/using-windows-10-for-serious-web-development-a-quick-starter-guide-18m7>

这很有趣，因为我遇到的每个其他开发人员总是想知道并问我，作为一名 JavaScript/前端开发人员，是否(以及如何)真的有可能在 Windows 机器上工作。和**地狱是啊，它是！**

先说一点我的个人历史吧。

我基本上一辈子都在用 Windows(这里先忽略 C64 和 Amiga 500)。首先，我用我爸爸工作时用的 Windows 3.11 机器玩扫雷游戏(实际上并不了解它是如何工作的(我仍然不明白))。后来，当我 11 或 12 岁时，我有了自己的第一台 Windows 95 个人电脑。不久之后，我迷上了游戏，所以买 Mac 对我来说并不是一个真正的选择。更不用说，当你 14-15 岁还在上学的时候，它们是(现在也是)负担不起的。

当我停止玩游戏的时候，我还在上学，所以我仍然买不起 Mac 电脑，当我后来在 2002 年找到第一份真正的工作时，他们给我提供了一台运行 Windows 操作系统的电脑。我在 2008 年认真考虑过换成 Mac，但当时我做了很多业余摄影和图像编辑，而且我用的是合法的(！)(学生)Photoshop 的拷贝只对 Windows 有效，所以我没有换。谢谢 Adobe！

我考虑在 2013 年再次改用 Mac 电脑(我甚至买了我自己的第一台 Mac 电脑，MacBook Air)，但出于我不记得的原因，我仍然坚持使用我的 Windows PC。

这就是我今天的处境。作为一名 Javascript 开发人员，我仍然在日常生活中使用 Windows 作为我的主要操作系统，即使我现在学会了如何在 Mac 键盘上键入@字符，我也不会再考虑改用 Mac 了。至少短期内不会。

我有一个可以使用的键盘，一大堆可行的硬件，而且价格都很合理。Windows 10 变得越来越好，微软在过去几年里一直在做一项令人惊叹的工作，并将继续这样做。

那么让我们回到最初的问题:**作为一名 JavaScript 开发人员，如何可能在 Windows 机器上工作？**

## 首先:使用专业版

许多电脑都配有 Windows 10 家庭版，这对专业人士来说有点儿没用，因为你不支持使用 Docker 所需的 **Hyper-V** ，也不支持 WSL，即 **Windows Linux 子系统**。所以首先:获得正确的版本。如果你买了一台装有 Windows 10 Home 的 Windows PC，不要担心:你可以很容易地通过 Windows 商店进行升级。我曾经做过一次，不记得我是怎么做的，但我记得的是，它花了我一次谷歌搜索，五次点击和 15-20 分钟的时间，我的系统从家庭升级到专业。所以这真的很简单*。*

## 工具

这是最重要的部分。Mac 上的 iTerm 或者 Homebrew 之类的工具简直不可思议。Windows 赶不上这一速度，但在我看来，有一些替代产品至少和 macOS 产品一样好。

### 小天鹅

让我们从控制台开始。忘记 Powershell 或 Windows 命令外壳(cmd)。我在 CLI 上花了相当多的时间，但我已经多年没有使用它们了。使用[T5】CygwinT7】！这是一个*兼容层*，给你(他们自己的话)“Windows 上的 Linux 感觉”。您不能在 Cygwin 中运行原生 Linux 二进制文件，但是该项目将一大堆 Unix 工具移植到了 Windows 上，从我的经验来看，它们中的大多数看起来和感觉起来都像它们的原生 Unix 对应物。你甚至有一个主文件夹，你可以像在 linux 上一样通过在你的 Cygwin CLI 中键入`cd ~`来访问，你还有`ls`和`cat`，甚至`rm`(小心！).](https://www.cygwin.com/)

下载[安装程序](https://cygwin.com/install.html)，运行它，选择一个镜像来下载包列表，选择所有你喜欢的 Unix 包(例如 vim 或 nano，openssh，wget，curl，…)然后开始！不要让**而不是**安装 Git。我们稍后将手动执行该操作。还有用于 Windows 的**Git**，它集成得更好，造成的压力更小。

**提示:**与 Linux 不同，Windows 对于文件和文件夹是不区分大小写的。在使用 WSL Linux Shell 或 Git 时，我避免在路径中使用空格，并尽量保持所有路径都是小写，以免遇到麻烦。例如，我的 Cygwin 安装位于 C:\cygwin64 中。它可以帮你省去一些额外的麻烦。

一旦安装了 Cygwin，我建议安装 [apt-cyg](https://github.com/transcode-open/apt-cyg) ，你可能已经猜到了，它的工作方式与 Debian 上的 apt-get 非常相似。一旦它被下载到你的主文件夹(C:\cygwin64\home[username])，你就可以像`apt-cyg install curl`一样简单地安装软件包了。

**注意:** Cygwin 将其 bin 文件夹添加到您的系统路径中。如果系统路径被修改，您通常必须关闭所有 cmd.exe、powershell.exe、bash.exe 和 explorer.exe 进程，然后重新启动它们，以便在任何地方都可以使用更新的路径。最简单的方法通常是重启整个系统(嘿，还是 Windows …)。

//编辑:你可以在你的 cmd 里输入`refreshenv`，正如 [@AkosLukacs](https://twitter.com/AkosLukacs/status/1125645500085153792) 指出的！

当我在 Cygwin 中进行 Node.js 或 React 开发时，95%的日常任务都运行得非常好，它们看起来和感觉起来几乎像一个真正的 Linux。网络包，纱，NPM，木偶，节点。我甚至定期使用 Cygwin 的 openssh.exe ssh 到不同的服务器。你可以把所有的 ssh 密钥放在那里。如果你试图通过 ssh 连接到服务器，Cygwin/openssh 会自动寻找密钥，就像在 Mac 或 Linux 上一样！

我认为 Cygwin 应该为这些年来没有错过一台真正的基于 unix 的机器负责。

### Windows Linux 子系统(WSL)

有时候(真的很少，至少在我的案例中)Cygwin 提供的工具是不够的。然后是安装 **Windows 子系统 Linux** 的时候了。这是一个真正的运行在 Windows 机器上的 Linux 环境。一旦你[在控制面板](https://winaero.com/blog/enable-wsl-windows-10-fall-creators-update/)中启用了这个功能，你就可以从微软商店中选择你的发行版，并运行几乎所有你可以在原生 Linux 机器上运行的东西，除了 GUI 应用程序([，尽管有人得到了这个功能](https://virtualizationreview.com/articles/2017/02/08/graphical-programs-on-windows-subsystem-on-linux.aspx))。目前你可以在 Ubuntu、Debian、SUSE、Kali、Pengwin 和 Alpine 之间进行选择。

**警告:**默认情况下，所有文件和文件夹的所有者都是 root，chmod 777，你知道，这很糟糕。您必须重新安装驱动器才能使用正确的文件和文件夹权限。请在此阅读如何做到这一点:

*   [https://dev blogs . Microsoft . com/command line/chmod-chown-wsl-improvements/](https://devblogs.microsoft.com/commandline/chmod-chown-wsl-improvements/)
*   [https://dev blogs . Microsoft . com/command line/automatically-configuring-wsl/](https://devblogs.microsoft.com/commandline/automatically-configuring-wsl/)

WSL 还附带了一个名为`bash`的二进制文件，和 Cygwin 一模一样。为了避免混淆，我为 WSL bash 添加了一个别名到我的`~/.bash_profile` :

```
alias lx=/cygdrive/c/Windows/System32/bash.exe 
```

这允许我在 Cygwin bash 中键入`lx`来启动 WSL bash。

`/cygdrive/c/`是您所有文件所在位置，Cygwin 将您的驱动器挂载到一个 unix 风格的文件系统，其中`/cygdrive/c`相当于 Windows 中的`C:\\`。

### 去吧

正如我之前提到的，当使用 Cygwin 附带的 Git 二进制文件时，有时会导致奇怪的行为和不同程序之间的不兼容，所以你最好下载 [Git for Windows](https://gitforwindows.org/) 。

如果你是 Windows 新手，安装过程中的选项可能会有点多。您不需要 Windows Explorer 集成(稍后我们将使用 Cygwin 和 ConEmu ),因此您可以在第一个屏幕中使用默认组件。在下一个屏幕中，您可以选择您最喜欢的编辑器来使用 Git。我选择默认的 nano，因为我从来没有像其他人一样理解 vim。随便选点别的吧。

在下一个屏幕中，您想使用“从命令行和第三方软件 Git”。而且你想用 OpenSSH 代替 TortoisePlink。下一个重要的选项是行尾转换。按原样签出，提交 Unix 风格的行尾(`core.autocrlf=input`)是您想要的，否则您的同事会对您发火，因为 GitHub 将行尾样式的所有更改都标记为拉请求的更改。这是丑陋的，你真的不想这样。

由于终端 MinTTY 可能是一个比 cmd 更好的选择，虽然我从来没有使用它，因为 Cygwin。在最后一个屏幕上，额外的选项，你应该检查所有三个:文件系统缓存，git 凭证管理器和符号链接。一旦安装完毕，你就可以使用所有你习惯的命令了。你甚至可以把一个`.gitconfig`文件放到你在 Cygwin 的主文件夹中，并在那里修改你所有的设置。它也尊重您在`~/.ssh`中的 ssh 密钥——就像在 Linux 或 Mac 上一样！

### 终端:ConEmu 或 Cmder

macOS 有 iTerm，很好看但是 Windows 有 [**ConEmu**](https://conemu.github.io/) 和 [**Cmder**](https://cmder.net/) (引擎盖下用的就是 ConEmu)。就我个人而言，我正在使用 ConEmu，它非常棒。它给了你很多键盘快捷键，支持你的控制台窗口的标签，并提供了很多方便你使用的功能和特性。它不如 iTerm 漂亮，但提供了非常相似的功能。我将 ConEmu 设置为默认的终端应用程序，在 ConEmu 中，我将 Cygwin 的 bash.exe 设置为默认 shell。Cygwin 自动检测所有已安装的 shell，您可以从下拉菜单中选择您想要的 shell！

你也可以将 ConEmu 注册为文件和文件夹的菜单选项，这样每当你在 Windows 的任何地方右击一个文件或文件夹时，你都可以选择“在这里打开 ConEmu”来启动一个指向所选路径的新的终端窗口。您可以在“集成”菜单的 ConEmu 设置中找到它。

### IDE/编辑器

我使用 **VSCode** 作为我开发的主要编辑器/IDE。因为 VSCode 来自微软，所以它在 Windows 上工作得非常好，没有任何问题(谁能想到呢？).如果你想使用来自 Cygwin 的 bash.exe 的集成终端(我就是这么做的，我也很喜欢)，你可能首先要调整一些设置:

```
"terminal.integrated.shell.windows": "C:\\cygwin64\\bin\\bash.exe",
"terminal.integrated.env.windows": {
  "CHERE_INVOKING": "1"
},
"terminal.integrated.shellArgs.windows": [
  "-l"
], 
```

这使得在 VSCode 中打开终端并使其指向当前项目的根文件夹成为可能。在切换到 VSCode 之前，它在我使用的 Atom 中的工作方式非常相似。

如果您使用的是 Windows 10 Build 18309 或更高版本，您还可以设置:

```
"terminal.integrated.windowsEnableConpty": true, 
```

这使得 VSCode 使用 ConPTY 而不是 Winpty。Rich Turner 告诉你更多关于 T1 的事情。

### Node.js

如果你正在使用 Node.js，我推荐使用 [nvm-windows](https://github.com/coreybutler/nvm-windows) 来安装它。它的工作方式与 **nvm** 在基于 Unix 的机器上的工作方式非常相似，但一些命令略有不同(例如`nvm list available`而不是`nvm ls-remote`)。

从 GitHub 下载安装程序，安装这个工具，你可以通过命令行输入`nvm install 10.15.3 && nvm use 10.15.3`来安装 Node.js。

升级预捆绑的`npm`版本有时会有点棘手。许多用户(包括我)在[这里报告了一个奇怪的问题](https://github.com/coreybutler/nvm-windows/issues/300)。我通过全局安装 yarn(`npm i -g yarn`)，将 yarn 的全局 bin 文件夹添加到路径中(你可以使用`yarn global bin`找到它)并通过`yarn global add npm`安装 npm 来解决这个问题。工作正常。只需确保纱线箱文件夹优先于 npm 的箱文件夹(因此在路径中将其向上移动)。

接下来你需要安装 [Windows 构建工具](https://www.npmjs.com/package/windows-build-tools)。这允许您使用`node-gyp`安装需要在您的机器上编译/构建的包。这是你真正需要 Powershell 的极少数情况之一。并且您需要一个具有管理权限的提升的 Powershell。在 ConEmu 中，你可以按[win]+[shift]+[w]打开一个“创建新控制台”对话框。在那里，您可以选择 Powershell 并勾选对话框底部的“以管理员身份运行”复选框。如果您没有使用 ConEmu，您可以按下[win]+[x]打开一个菜单，您可以从该菜单以管理员身份启动 Powershell 窗口。

控制台打开后，运行`yarn global add windows-build-tools`。这可能需要一两分钟。

### 码头工人

这很简单。Windows 有 [Docker。安装它，运行它，快乐。](https://docs.docker.com/docker-for-windows/install/)

不完全是。您需要首先启用 Hyper-V。[以下是你如何做的](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)。

**注意:**如果启用了 Hyper-V，早于 6.0.0 的虚拟盒子版本不再工作。如果你需要虚拟盒子 5.x 你不能同时使用 Docker。Hyper-V 和 Docker **或者**虚拟盒子 5.x .选一个。

### 表情符号

非常重要，尤其是当你来自 macOS 的时候。Windows 上的表情符号支持仍然是相当新的，不可否认的是，他们的表情符号实在是太丑了。在大多数应用程序中，您可以按[win]+[键。]打开表情选择器。它在 Chrome 或 VSCode 等“现代”应用程序中工作得很好，在终端、记事本或写字板等其他应用程序中工作得不太好(甚至根本不工作)。

### 其他不相关的工具

值得一看的工具，可进一步改善您的 Windows 体验:

*   将你的窗口组织成标签。
*   IrfanView-伟大的免费软件，可以打开(并转换)所有不同类型的图像
*   [记事本 2](http://www.flos-freeware.ch/notepad2.html)–作为 Windows 原生记事本的强大、轻便和快速的替代品。
*   [锁定计数器](https://lockhunter.com/)–查找(并退出)锁定文件的进程

当你设置一台 Windows PC 来使用它进行软件开发时，可能有许多其他的事情要知道和做，我在这里没有提到。如果有任何问题，请联系我！