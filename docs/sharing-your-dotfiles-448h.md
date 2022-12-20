# 共享您的点文件

> 原文：<https://dev.to/samerickson/sharing-your-dotfiles-448h>

## 为什么每个人都应该有自己的点文件库

一个好的木匠会在他们的工具和工作空间上投入时间和金钱。他们这样做是为了改善工作流程，让自己的生活更轻松，为什么不呢？

那么，为什么您不想花几分钟来设置您喜欢的编程环境呢？程序员没有理由不这样做。与 wood shop 相比，编程工作区的最大优势是建立一个点文件库和配置开源软件可以免费完成。不过要小心，很容易陷入“欺骗”的世界，把所有的时间都花在改变你的配色方案上，而不是真正有效率地写一些有用的东西。

对于点文件，总是有一条细微的界线，即我的配置应该走多远才能使编程速度最大化，但仍然比什么都不配置需要更少的时间？

## 点文件入门

我目前正在做一个项目，帮助那些不知道从哪里开始设置他们的点文件的新用户。目前，该项目正专注于 [swaywm](https://swaywm.org/) ，这是一个平铺窗口管理器，但后来我希望有一个关于配置大量软件和窗口管理器的指南，以便用户可以在安装点文件时挑选他们想要的东西，但我在这一点上离达到目标还有很长的路要走[链接到项目](https://github.com/samerickson/dotfiles)。

这很好，但这个项目是为了让你从锅炉板文件开始，然后你应该微调自己。让 git 存储库开始跟踪你的点文件的每个状态是一个好主意。这样，如果你打破了什么东西，你总是可以回到工作状态。

在 [Atlassian](https://www.atlassian.com/git/tutorials/dotfiles) 上有一个很好的关于如何启动和运行的指南，但是如果你不想了解正在发生的事情(我强烈建议这样做)，你只想复制和粘贴工作代码(实际上你不应该用**任何你不**完全了解**功能的代码**来做这件事，因为它可能对你的系统安全有不利影响，更不用说破坏东西了)，下面是代码:

### 第一步:安装我的点文件库(如果您喜欢使用自己的库，请跳过)

**注意:**这样做会覆盖您的主目录中与我的点文件存储库中的文件相匹配的任何现有文件。

```
git clone https://github.com/samerickson/dotfiles.git
cd dotfiles
install.sh 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:设置个人存储库

**初始化 git 裸存储库。**

```
git init --bare ~/.dotfiles 
```

Enter fullscreen mode Exit fullscreen mode

**创建一个别名来访问存储库。**
这使得`dotfiles`的功能与`git`相似，但与`.git`有几个重要的区别。同样，如果你想了解更多，请查看 Atlassian 关于这个主题的教程。

```
# Add this to your ~/.bashrc
alias dotfiles='/usr/bin/git --git-dir=$HOME/.dotfiles --work-tree=$HOME'

# Source your ~/.bashrc
source ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:添加您的文件

现在是时候添加你想要包含在你的*点文件库*中的所有文件了(非常小心，不要上传任何你不希望公开的信息，因为你添加的**文件最终会在 github** 上公开。请勿添加和`pgp`键、`ssh`键、密码或个人信息)。

```
# The syntax is just like `git add` but instead of `git`, we write `dotfiles`
dotfiles add .bashrc
dotfiles add .profile 
```

Enter fullscreen mode Exit fullscreen mode

### 第四步:设置 Github 端的东西

前往 [Github](https://github.com/) 并登录。接下来，创建一个新的存储库。然后 Github 会给你指示如何"...从命令行推送现有存储库”。按照这些指示，你就完成了。