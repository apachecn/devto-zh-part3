# 面向开发人员的新 MacBook 设置

> 原文：<https://dev.to/therealdanvega/new-macbook-setup-for-developers-2nma>

[![MacBook Setup](img/1e7d56e8727fc96dc398ae6a6fad34eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BcXcD88z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5sn1ah1x2i3kh1pqi1kg.jpg)

我想我会花些时间记录下我得到一台新机器后所做的事情。这将介绍我配置的一些首选项、我安装的应用程序以及我用于不同程序的设置。我总是很好奇其他软件开发人员是如何设置他们的开发机器的，所以我想这将是一个分享我的好机会。

我是一名软件开发人员，所以我的大部分配置将围绕编程。我目前的 MacBook 配置如下:

*   MacBook Pro (13 英寸，2017 年，两个雷电 3 端口)
*   处理器:2.3 GHz 英特尔酷睿 i5 处理器
*   内存:16 GB
*   启动盘:Macintosh HD
*   显卡:英特尔 Iris Plus 显卡 640 1536 MB
*   存储:500 GB

当你打开 MacBook 的电源时，你将运行设置。我通常在这里配置我能配置的任何东西，比如 Wi-fi 和 Apple ID。其余的说明将假设您已经完成了设置，并且在桌面上。

*如果你不想等待磁盘加密(macOS 更新部分的错误),请不要在设置期间选择，稍后再执行*

## macOS 更新

我做的第一件事是运行任何可用的更新。在我的情况下，笔记本电脑预装了 macOS Sierra，而 macOS Mojave 已经发布。

所以我的第一步是升级到 macOS Mojave。你可以从 App Store 下载并运行它。

*错误:您可能无法安装到此宗卷，因为它当前正在被加密*

如果你得到上述错误，那么你选择了在安装过程中加密硬盘驱动器的选项。确定您已接通电源，然后前往“系统偏好设置”>“安全与隐私”>“文件保险箱”。从那里你可以看到加密的进程，当它完成时，你可以启动 macOS Mojave 安装程序。

## 应用商店

如果您使用 Apple Id 登录，您将被直接带到 Apple Store。这是运行任何可用更新的好时机。

你可能会遇到问题，因为下载它们的 apple id 不是你。在这种情况下，我只是移除了那些我不使用的应用程序(GarageBand、Numbers、Pages、iMovie...)

为了删除应用程序，我喜欢安装 AppCleaner。这将确保应用程序和任何相关文件都将被删除。

我非常喜欢的另一个应用是[清理我的 Mac](https://macpaw.com/cleanmymac)

### 车库乐队

遗憾的是，AppCleaner 仅适用于第三方安装的应用程序，不会移除 GarageBand。您需要手动删除它，虽然还有几个位置，但这是您应该删除的 3 个主要位置。如果你要在其他程序中使用苹果音效，请在删除前仔细阅读。

*   /Applications/GarageBand.app
*   /资源库/应用程序支持/GarageBand/
*   /资源库/音频/Apple Loops/Apple/

倒垃圾

## 入门

我会用自制软件安装任何我能安装的东西。在你安装家酿之前，你需要安装 Xcode 命令行工具。打开一个新的终端，键入以下命令。即使你计划安装 Xcode，我相信你仍然需要现在安装它们，因为它们已经从标准安装中移除了。

```
xcode-select --install 
```

命令行工具包是一个独立的小软件包，可以从 Xcode 单独下载，允许您在 OS X 中进行命令行开发。它由两个组件组成:OS X SDK 和命令行工具，如 Clang，它们安装在/usr/bin 中。

### 自制

正如我前面说过的，我用自制软件来安装任何它能安装的东西。我最近发现了木桶，它让我的生活变得简单多了。如果你通常使用 brew 来安装像 google-chrome 这样的东西，你知道你必须把它拖到 applications 文件夹中。如果你使用木桶，它不仅会下载软件包，而且会把它移动到应用程序文件夹。

[家酿网站](https://brew.sh/)

安装:
/usr/bin/ruby-e " $(curl-fsSL[https://raw . githubusercontent . com/home brew/install/master/install)](https://raw.githubusercontent.com/Homebrew/install/master/install))

安装后

*   如果您需要 brew 帮助，可以运行 brew 帮助。
*   brew 更新-你不应该有任何东西要更新，但最好检查一下。
*   酿造品搜索“术语”来搜索酿造品

## 终端/ Bash / iTerm

现在我们已经安装了自制软件，是时候开始安装一些软件了。bash 的默认版本是 3.2，我想继续升级到 4.x。这里有一个非常好的指南，你可以按照它来升级 bash。

*   bash -v (bash-3.2)
*   brew 安装 bash
*   如果你关闭终端或打开一个新标签，它会显示 4.4，但这仍然不是默认版本。
*   哪个 bash 会显示你正在使用的 bash。

现在我们已经更新了 bash，我们需要将它作为我们的默认 shell。为此，您需要编辑/etc/shell

```
sudo vi /etc/shells
add the path /usr/local/bin/bash
comment out the others

Change to the new shell
chsh -s /usr/local/bin/bash 
```

### 痛击简介

现在我们已经更新了 bash，我需要定制我的 shell。查看您的主目录，看看您是否有一个. bash_profile，如果没有，您可以使用下面的命令创建一个。

```
touch .bash_profile 
```

您可以在这里向 bash 添加各种定制。我已经包括了我的。bash_profile ,如果您想查看的话。像往常一样，如果你对里面的东西有疑问，请告诉我。

### iTerm2

我使用终端的时间最长，甚至有几个人在上面叫我出去😂

我现在全职使用 iTerm2，我非常喜欢它。如果你想了解它的一些功能和配置，请[访问他们的网站](https://www.iterm2.com/features.html)。

```
brew cask install iterm2 
```

我喜欢做的一件事是定制颜色，一个很好的资源是[https://iterm2colorschemes.com/](https://iterm2colorschemes.com/)

这是我最喜欢的配色方案列表。

*   德拉库拉
*   FirefoxDev
*   开源代码库
*   葡萄
*   草
*   潮人绿
*   公司自产自用
*   手册页
*   材料
*   材料标记
*   小说
*   海洋材料

## 开发设置

现在我已经有了一个漂亮的命令行，里面充满了各种特性，是时候开始安装我将使用的所有不同的应用程序了。如果你对这些有任何疑问，或者我为什么要安装它们，请见下面的联系我部分。

*   brew 安装 git
*   酿造桶安装谷歌浏览器
*   酿造桶安装谷歌浏览器金丝雀
*   酿造桶安装火狐
*   brew cask 安装 Firefox-开发人员版
*   酿造桶安装 visual-studio-代码
*   brew cask 安装 visual-studio-code-insiders
*   酿造桶安装智能理念
*   brew cask 安装 eclipse-java
*   酿造桶安装邮递员
*   酿造桶安装码头
*   酿造桶安装眼镜
*   brew 安装树
*   [https://github.com/sindresorhus/quick-look-plugins](https://github.com/sindresorhus/quick-look-plugins)

### Visual Studio 代码

如果你没有使用 Visual Studio 代码，你还在等什么？玩笑归玩笑，我相信你现在可能已经听说过代码，这里有一些我定制它的方法。

#### 分机

我过去讨厌在新机器上安装 Visual Studio 代码。原因是我越来越喜欢这个编辑器，以至于我可以扩展任何东西。我过去常常一个一个地安装它们，当你不得不这样做 35 次时，它就变得很旧了。

如果您想获得当前安装在您机器上的扩展列表，您可以使用下面的命令。

```
code --list-extensions 
```

它的好处是您可以使用命令行安装 visual studio 代码扩展。

```
code --install-extension ${extension-name} 
```

你可以把你的列表的结果放到一个文件中，或者如果你愿意的话，你可以从这个库中获取我的结果。一旦你有了这个文件，你可以运行下面的命令来安装你所有的扩展。如果你对我正在使用的扩展感兴趣，我已经将[包含在我的扩展列表](https://github.com/cfaddict/new-macbook-setup/blob/master/vscode-extensions.txt)中。

```
cat extensions.txt | xargs -L1 code --install-extension 
```

在我这样做并在推特上发布之后，很多人让我知道了另一个叫做同步设置的扩展。这将跨计算机同步您的所有 visual studio 扩展、设置、键绑定等。

[https://marketplace.visualstudio.com/items?itemName = shan . code-settings-sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)

#### Fonts

我是单声道字体的超级粉丝。我安装了这个，并在我的大部分开发中使用它。如果我在写文档，我有时会使用不同的字体，这取决于。

[潮湿的单声道](https://dank.sh/)

#### 用户设置

我已经包括了我的用户设置，以防你想看我用什么。我这里没有太多，但我会尽快更新这些。

### 节点& NPM

如果你要安装节点，我认为最好的方法是使用[节点版本管理器(NVM)](https://github.com/creationix/nvm) 。对我来说，这比从 brew 安装或者直接从他们的网站下载有一些优势。

*   您可以安装多个版本的节点
    *   您可以设置默认版本
    *   您可以轻松地在这些版本之间切换
*   安装在您的主目录中
    *   你不需要特权
    *   全局安装软件包时不再需要 sudo

一旦有了 NVM，您就可以使用以下命令安装最新的稳定版本(在撰写本文时为 v10.12.0)。

```
nvm install stable 
```

#### 要安装的全局包

```
npm install -g tldr
npm install -g typescript
npm install -g @vue/cli
npm install -g vuepress
npm install -g @angular/cli
npm install -g eslint
npm install -g gitbook-cli
npm install -g lodash 
```

### Git 配置

通常会默认安装 git，但我们之前使用 brew 安装了最新版本。现在我们已经使用了最新版本的 git，我们需要做一些配置。

.gitconfig

*   git config-global user . email "[dan@techelevator.com](mailto:dan@techelevator.com)
*   git 配置-全局用户名“Dan Vega”
*   别名
    *   git config-global alias . add-commit '！' git 添加-A && git 提交'

### 数据库

*   [PostgreSQL](https://www.postgresql.org/)
*   [Mongodb](https://www.mongodb.com/)
*   [MySQL](https://www.mysql.com/) 的实现

#### PostgreSQL

安装 PostgreSQL 最简单的方法是使用自制软件。

```
brew install postgresql 
```

安装完成后，您可以使用以下命令让它自动启动。

```
brew postgresql-upgrade-database 
```

我不经常需要它，所以当我想运行它时，我可以运行下面的命令:

```
pg_ctl -D /usr/local/var/postgres start 
```

更好的是，我可以在我的 bash 概要文件中添加一些别名来使这变得更加容易。

```
alias start_postgres="pg_ctl -D /usr/local/var/postgres start"
alias stop_postgres="pg_ctl -D /usr/local/var/postgres stop -s -m fast"
alias pgup="start_postgres"
alias pgdown="stop_postgres" 
```

我们的学生也使用 DBVisualizer，所以我也想安装它。

```
brew cask install dbvisualizer 
```

#### MongoDB

#### [MySQL](#mysql)

要开始使用 MySQL，运行以下命令:

```
brew install mysql 
```

让 MySQL 在电脑启动时自动运行:

```
brew services start mysql 
```

或者您可以手动启动/停止它

```
mysql.server start 
```

为了与我们的 PostgreSQL 保持一致，我们可以创建一些别名。

```
alias start_mysql="mysql.server start"
alias stop_mysql="mysql.server stop"
alias mysqlup="start_mysql"
alias mysqldown="stop_mysql" 
```

### SDKMan

这是我最喜欢的版本管理器之一，因为我使用了它管理的许多软件开发工具包(SDK)。如果你没听说过 SDKMan，请点击这里查看。这是我使用 SDKMan 管理的 SDK 列表。

*   Java 语言(一种计算机语言，尤用于创建网站)
*   绝妙的
*   Grails
*   Gradle
*   专家
*   微型机器人
*   Spring Boot
*   我的锅

这里是 SDK 的完整列表[https://sdkman.io/sdks](https://sdkman.io/sdks)

**安装:**curl-s "[https://get . sdkman . io](https://get.sdkman.io)" | bash

如果你只是输入 sdk install candidate，它将安装最新的稳定版本，或者你可以安装一个特定的版本

```
sdk install java 8.0.191-oracle 
```

如果你需要得到一个版本列表，你可以要求它:

```
sdk list java 
```

### 浏览器配置

打开同步并登录 chrome，这将带来我所有的书签和扩展。如果你对这些有任何疑问，或者我为什么要安装它们，请见下面的联系我部分。

**扩展**

*   最后一遍
*   语法上
*   颜色选择器
*   肝负荷
*   子块来源
*   隐私獾
*   oneTab
*   json 查看器
*   devtools 视图

## 系统偏好

我在偏好设置中定制了一些东西:

**触控板:**
系统偏好>触控板>滚动&缩放:
我取消了滚动方向:自然(我感觉不自然)

**Dock:**
系统首选项> Dock
将尺寸调小并打开放大功能
我还从 Dock 中移除了所有不用的图标

**头像:**
系统偏好>用户&群组>编辑头像

**主题:**
macOS Mojave 推出光明&黑暗主题。我仍然坚持浅色主题，即使我在几乎所有的编辑器或程序中使用深色主题。如果你以后想改变这一点，只需进入系统偏好设置>常规>外观

## 查找器

我喜欢自定义 Finder，这样我就可以快速找到我最常去的地方。

**地点**

*   将 Macintosh HD 添加到位置，这样我就可以随时访问根硬盘
*   主页/用户/织女星
*   截屏(配置截屏实用程序以保存在此)
    *   打开屏幕截图>选项>其他位置

**finder 中的一些提示**

*   cmd+shift+h(带你回家)
*   cmd。(显示隐藏的文件和文件夹)

## 工作设置

这些是我工作所需的应用程序和配置列表。你可能不需要这些工具，但我想我会在这里分享它们。

最后一关
Slack
Twitter(应用商店)
Screenflow

### Adobe 创意套件

我是 Adobe 及其所有产品的超级粉丝！

*   Photoshop CC
*   Illustrator CC
*   Premiere Pro CC
*   后效 CC
*   首映紧急抄送
*   无红利(ex-dividend)

### Windows 10

在工作中，我需要使用 macOS 和 Windows 10 进行双重启动。我们有一个学生使用 Windows (Visual Studio、SQL Server 等)的. NET 课程...)而我需要能够养活他们。最棒的是苹果用 BootCamp Assistant 让这个愚蠢的事情变得简单了。如果你想了解更多，请看下面的文档。

# 新兵训练营 Windows 10

本文将概述在 Mac OS 上安装 Windows 10 需要做的事情。你应该做的第一件事是查看苹果的官方支持文档。本文档概述了您在开始之前应该知道的事情，从哪里获得 Windows 10 ISO 映像以及让一切正常工作所需的步骤。

[https://support.apple.com/en-us/HT201468](https://support.apple.com/en-us/HT201468)

## 出发前

*   您将需要足够的可用磁盘空间来创建最小大小为 64GB 的 Boot Camp 磁盘分区。为获得最佳体验，建议使用 128GB。
*   拔下任何外部设备，如键盘、鼠标和显示器。
*   插上电源，以防你离开电脑。你最不希望发生的事情就是电脑在安装过程中死机。

**步骤**

*   下载 Windows 10 ISO
*   运行 Boot Camp 助理
*   选择分区(建议 128GB)
*   安装 Windows

* *如果你通过 ISO 下载向导，它说“Windows 10 教育英语”,不要担心。当我们安装 Windows 时，我们可以选择我们想要的版本(教育/家庭/专业)。*

## Windows 安装

创建分区并重新启动到 Windows 后

*   选择您的语言
*   输入您的产品密钥(如果您没有产品密钥也可以，选择我没有产品密钥)
*   选择 Windows 10 版本(Win 10 Home)
*   接受许可条款(阅读每个单词)
*   当你被要求创建一个用户名时，不要在你的名字中加空格。当你创建一个用户名时，它将是用户主文件夹/Users/Dan Vega/的名称。我不知道你怎么想，但我不喜欢太空。
*   输入密码和安全问题/答案
*   Cortana(我关掉)
*   隐私设置(我把这些都关掉了)

后 Windows 安装

*   在 Windows 上安装 Boot Camp(建议重启后重新启动)
*   连接到无线网络
*   运行 Apple 软件更新(开始菜单)(需要重新启动)
*   更新日期/时间设置
*   启用开发者模式
*   激活 Windows(如果您有产品密钥)
    *   个性化窗口

## 软件安装

我没有在这里安装一套完整的应用程序，因为我没有全职使用它。

*   [谷歌浏览器](https://www.google.com/chrome/)
*   [Visual Studio 2017 社区](https://visualstudio.microsoft.com/downloads/)
    *   选择 ASP.NET 网站开发(这将给你。网络核心)
*   [SQL Server 2017 速成版](https://www.microsoft.com/en-us/sql-server/sql-server-editions-express)
*   [SQL Studio 管理工作室(SSMS) 17.9 GA](https://docs.microsoft.com/en-us/sql/ssms/sql-server-management-studio-changelog-ssms?view=sql-server-2017#ssms-179-latest-ga-release)
*   Windows 上的 Ubuntu(如果你想要 bash 的话)
*   [Visual Studio 代码](https://code.visualstudio.com/)

## 操作系统之间的切换

如果您想要在操作系统之间切换，您需要重启电脑并按住 option 键。当计算机启动时，它会问你想从哪个分区启动。一次只能运行一个操作系统，不能同时运行两个操作系统。这对于我的用例来说完全没问题，但是如果你想同时运行两者，请查看 [Parallels](https://www.parallels.com/products/desktop/)

## 联系我

如果您对我正在使用的任何东西有任何疑问，或者对我遗漏的东西有任何建议，请不要犹豫，评论或联系我。一如既往...

快乐编码

丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*