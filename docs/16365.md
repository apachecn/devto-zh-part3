# 如何从命令行打开文件和文件资源管理器- Windows

> 原文：<https://dev.to/jamiemccarville/how-to-open-files-and-file-explorer-from-the-command-line---windows--17a7>

## 终端升级

上个月，我钻进兔子洞，在我的 Windows 笔记本电脑上设置我的终端。我一直在使用 Git bash 来完成我所有的 git & Github 工作，但是没有太多关于命令行的其他经验，并且想要提高我的技能和升级我的工具。作为起点，我观看了 Wes Bos 的免费视频系列，名为[“命令行高级用户”](https://www.commandlinepoweruser.com)本视频系列的目标是让您开始使用 Zsh 和 oh-my-zsh。我还使用了迪伦·丰塔纳的 GitHub repo[“CmderZSH”](https://gist.github.com/dfontana/3e27ec5ea3a6f935b7322b580d3df318)来帮助解决在 Windows 上安装的一些细节问题。我决定用[电力线 9k](https://github.com/bhilburn/powerlevel9k) 主题来增加一些定制。

## 我的当前设置

我不会进入设置这一切的过程，因为上面的链接可以帮助你。话虽如此，以下是我目前的设置:

*   [Cygwin](https://www.cygwin.com/) ("...提供类似于 Windows 上的 Linux 的功能”)
*   mintty(Cygwin 的默认终端仿真器)
*   [命令器](http://cmder.net)(控制台模拟器)
*   Zsh (Z 外壳)
*   [oh-my-zsh](https://ohmyz.sh/) (管理 zsh 配置的框架)
*   电力线 9k 主题

## 打开文件和文件浏览器

消费后(超出预期！)时间设置好了一切，但仍有一些东西我在努力工作。在 Wes 的视频中，他展示了使用“打开”命令在代码编辑器中打开文件的例子。我无法让它为我工作，在尝试了一些不同的东西都失败后，我离开了它，后来又回来了。

## 解

经过一番搜索，我找到了我一直在寻找的答案。在 Windows 中，我们有一个类似于“打开”的命令，那就是“资源管理器”命令。下面是该命令运行的几个例子。

### 打开文件浏览器

[![](img/61e12072b41bc64be48d518cde74cddb.png)](https://cl.ly/ff63b5fe222e)

### 打开当前目录下的文件浏览器

[![](img/47d8f76643a2b170ca6ebbef272676e4.png)](https://cl.ly/0ddd8e6db00e)

### 用默认程序打开文件

[![](img/3876370255b1350d8ed811e8ec521c7b.png)](https://cl.ly/944244a932e0)

### 用 VSCode 打开文件

*命令不同但功能相似
[![](img/18e6af90e400b0cde90491010f14ec29.png)](https://cl.ly/4e27b1347331)

这是我开始学习编码以来的第一篇博文！我从这么多的帖子和文章中受益匪浅，我希望我能够通过分享我在钻研 JavaScript 和努力获得全职远程开发工作时所学到的东西来回报。祝每个人在 2019 年的目标上好运，我希望很快再次发布。