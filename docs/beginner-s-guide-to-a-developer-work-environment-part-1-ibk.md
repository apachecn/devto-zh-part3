# 开发人员工作环境初学者指南(第 1 部分)

> 原文：<https://dev.to/ikey2244/beginner-s-guide-to-a-developer-work-environment-part-1-ibk>

当我第一次接触编程时，我对专业开发人员是如何工作的一无所知。我想写点东西的想法是打开[https://codepen.io/](https://codepen.io/)然后开始破解。这帮助我很快做起来，但我知道这是不可持续的。我不断地问自己“开发者在现实世界中使用什么？”被所有的工具吓到了，我回到了我舒适的在线编辑世界。直到我加入了一个编码训练营，我才开始真正的工作流程。

我知道打开一个终端或编辑器，却不知道下一步该做什么是多么令人畏惧。所以这是我试图让初学者更容易开始使用专业开发人员使用的工具。

### 1。Xcode(MacOS)

#### 描述:

为了在你的电脑上建立一个合适的开发环境，你需要 Xcode。我不怎么用 Xcode，这只是入门所需。

#### 步骤:

1.  通过 mac 应用程序安装 Xcode

[https://itunes.apple.com/us/app/xcode/id497799835?mt=12](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)

### 2。Iterm2(MacOS)

#### 描述:

Iterm2 是 MacOS 中标准终端的替代品。下面是 Iterm 在普通终端上提供的所有特性的列表。
[https://www.iterm2.com/features.html](https://www.iterm2.com/features.html)

#### 步骤:

1.  安装 iterm 2
    T1】https://www.iterm2.com/

2.  保持打开状态，我们会回来并开始输入一些命令！

### 3。自制(苹果电脑)

[https://brew.sh/](https://brew.sh/)

#### 描述:

家酿允许你通过终端(Iterm2)安装包。

### 步骤:

1.)安装自制软件

粘贴到 Iterm2:
`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` 

2.)确认自制软件安装工作正常

粘贴到 Iterm2:
`brew --version`

应该会返回类似
的内容

```
Homebrew 2.1.4
Homebrew/homebrew-core (git revision 59240; last commit 2019-05-31)
Homebrew/homebrew-cask (git revision a6dcf; last commit 2019-05-30) 
```

### 4。Zsh(oh-my-zsh)(MacOs 或 Windows)

[https://ohmyz.sh/](https://ohmyz.sh/)

#### 描述:

Zsh 是一个 shell，它有很多特性使我们程序员的生活变得更容易。

oh-my-zsh 让您能够为 zsh shell 安装主题和插件！

一些最著名的

*   自动光盘:只需输入目录的名称
*   拼写纠正和近似完成:如果你在输入目录名时犯了一个小错误，ZSH 会帮你改正
*   插件和主题支持:ZSH 包括许多不同的插件框架

完整功能列表:[https://github.com/hmml/awesome-zsh](https://github.com/hmml/awesome-zsh)

### 步骤:

1.)用家酿安装 zsh

粘贴到 Iterm2:
`brew install zsh zsh-completions`

2.)将 Zsh 设置为默认 shell

粘贴到 Iterm2:
这需要你输入密码。这将是你用来登录你的电脑的同一个密码。
`chsh -s /bin/zsh`

3.)安装哦我的 zsh

粘贴到 Iterm2:
`sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`

### 5。Visual Studio 代码(MacOs 或 Windows)

#### 描述:

Visual Studio 代码是一个代码编辑器。代码编辑器有很多很好的选择(Atom、括号、Sublime 等等。)所有这些都是很好的选择，但是在本教程中，我将坚持使用 VS 代码。

### 步骤:

1.)安装 vs 代码
T1】https://code.visualstudio.com/Download

### 回顾

完成这些步骤后，您应该:

*   Xcode 已安装，它将您的电脑设置为开发环境
*   Iterm 安装，这给你一个比 mac 默认更好的终端
*   家酿安装，这使得它很容易通过网络安装软件包
*   Zsh(oh-my-zsh)安装，使您的终端超级用户友好和漂亮
*   Vs 代码安装，让您能够以一种非常直观的方式编辑您的代码。

我希望这能帮助那些像我第一次进入编程时一样苦苦挣扎的人。在第 2 部分中，我将展示如何在现实世界中使用这些工具。

### 免责声明

如果你有任何问题，请让我知道。开发环境有时很难建立。也请纠正我犯的任何错误。谢谢:)