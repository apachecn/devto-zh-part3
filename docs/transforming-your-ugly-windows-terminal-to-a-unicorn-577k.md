# 把你丑陋的 Windows 终端变成独角兽

> 原文：<https://dev.to/dylantientcheu/transforming-your-ugly-windows-terminal-to-a-unicorn-577k>

<figure>[![](img/c6754b9bddbbd2d496d094c09e9b5023.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AqJKEEld--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/853/1%2A9WodytFtET3BcDLM3VN28A.gif) 

<figcaption>这就是我们的目标</figcaption>

</figure>

你在 Windows 上，你经常听到其他平台的用户吹嘘他们的超级“彩虹吐槽”命令提示符。别担心，你也可以得到一个看起来很棒的终端。

下面列出了要遵循的步骤:

#### **1。安装 WSL**

[https://docs.microsoft.com/en-us/windows/wsl/install-win10](https://docs.microsoft.com/en-us/windows/wsl/install-win10)(安装 WSL 的简单明了的步骤)。

#### **2。安装您的终端**

以上结果是 Felixse 用 [Fluent 终端实现的。这个终端在 Windows 上非常棒，因为它平稳地取代了 Powershell 或 CMD，保留了所有的功能，并使您可以随时在两者之间轻松切换。它还具有出色的性能和漂亮的设置界面。](https://github.com/felixse/FluentTerminal)

<figure>[![](img/1a2752b53c340d666510bdba04d84c4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fikocem9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AYyPlr7EriMKf7iAS.jpg) 

<figcaption>我有说过尊重 [Windows 流畅的设计](https://www.microsoft.com/design/fluent/)吗？😍</figcaption>

</figure>

这个软件仍处于早期开发阶段，但它足够稳定(对我来说),可以作为默认终端使用。安装 Fluent 终端最简单的步骤是:

> 1.  T2] Install [chocolate](https://chocolatey.org/)
> 2.  从提升的/管理外壳程序中,执行乔科安装流畅终端

使用[超级终端](https://hyper.is/)(不考虑你的操作系统——因为超级是跨平台的)跟随[这篇来自](https://medium.com/@ssharizal/hyper-js-oh-my-zsh-as-ubuntu-on-windows-wsl-terminal-8bf577cdbd97) [Syed Sharizal](https://medium.com/u/ca206c3e0a4d) 的教程可以获得另一个漂亮的结果，这篇教程的灵感来源于此💪🏾。

#### 3。设置流畅的终端

<figure>[![](img/7dec3af9522bde472657b1d78189dcc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vLY4xFx3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/802/1%2AjnK7gA02cVdPkpM4VsPIwQ.png) 

<figcaption>选择您终端的配置文件</figcaption>

</figure>

为了获得具有 UNIX 功能的终端，我们必须切换到 WSL，原因很简单，这不能用 Windows 命令或 Powershell 来实现。

#### 4。安装 ZSH

*   运行 sudo apt-get 安装 zsh
*   打开 bash_profile vim ~/。bashrc——使用[这份备忘单](https://vim.rtorr.com)直接获取您的 vim 知识。
*   将您的 ZSH 设置为默认值(按照#注释来查找文件)。

```
bash -c zsh
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
case $- in
 \*i\*) ;;
 \*) return;;
esac 
```

#### 5。安装我的 ZSH

Oh My Zsh 是一个令人愉快的、开源的、社区驱动的框架，用于管理您的 Zsh 配置。它捆绑了数千个有用的功能、助手、插件、主题和一些让你大叫的东西…

要安装我的 ZSH，请运行以下命令。

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

#### 6。配置我的 ZSH

现在你已经有了马🐴。是时候让它成为独角兽了🦄。

通过下面的步骤，你可以根据自己的愿望和感觉随意配置一切；

*   **主题化您的 OMZ —安装 PowerLevel9K**

```
git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k 
```

在您的~/中选择此主题。zshrc 通过添加这一行

```
ZSH\_THEME="powerlevel9k/powerlevel9k" 
```

*   **安装电力线字体**

这些字体帮助您获得箭头和 git 图标，您可以在提示符的左侧看到它们。

我用的是书呆子字体 T1，而不是 T2 电力线字体 T3，因为我认为后者维护起来更简单。

**Nerd Fonts** 是一个用大量字形(图标)修补开发人员目标字体的项目。特别是从流行的“标志性字体”中添加大量额外的字形，如[字体牛逼](https://github.com/FortAwesome/Font-Awesome)、[图标](http://vorillaz.github.io/devicons/)、[图标](https://github.com/github/octicons)等。

1.  打开[http://nerdfonts.com/](http://nerdfonts.com/)
2.  前往下载，然后选择您的字体并下载
3.  像在 windows 上安装任何字体一样安装它
4.  将字体添加到流畅终端— ***设置→终端→字体家族***
5.  使用 vim 添加 NerdFont 兼容性。zshrc 然后在前面的 ZSH 主题下添加下面一行

```
POWERLEVEL9K\_MODE='nerdfont-complete' 
```

*   **进一步定制**

一旦你有了 powerlevel9k，一切都在你这边了。您可以根据自己的喜好进行配置。

这些资源可以帮助您定制

🦄——[我自己的。zshrc 配置](https://gist.github.com/blurdylan/eb32b3ddd1b9e8a7eaad134e348d2b42)

🦄— [Powerlevel9k 定制文档](https://github.com/bhilburn/powerlevel9k#prompt-customization)

🦄— [书呆子们展示他们的配置——以及配置文件](https://github.com/bhilburn/powerlevel9k/wiki/Show-Off-Your-Config)

#### 更…

如果有些信息对你来说听起来很陌生。记住，这个教程是从许多其他人那里得到灵感的，有些在下面

*   [在 Windows 10 上的 WSL 中使用 Zsh](https://medium.com/@vinhp/use-zsh-in-wsl-on-windows-10-5d439a749c4c)
*   [使用 Oh-my-Zsh + iTerm2](https://medium.com/swlh/power-up-your-terminal-using-oh-my-zsh-iterm2-c5a03f73a9fb) 给终端加电
*   [如何在 Windows 10 Creators 更新中用 WSL 设置好看的终端](https://medium.com/@Andreas_cmj/how-to-setup-a-nice-looking-terminal-with-wsl-in-windows-10-creators-update-2b468ed7c326)