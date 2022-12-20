# 与 Tmux 的持久 SSH 会话

> 原文：<https://dev.to/idoko/persistent-ssh-sessions-with-tmux-25dm>

## 你好 SSH

SSH(安全外壳)是一种广泛使用的协议，它可以帮助您进入远程系统，做您想做的事情，然后离开。这意味着，你可以从 android 设备远程检查你的服务器日志，从你的办公室机器监控躺在你卧室某处的覆盆子馅饼，或者进入完全黑客模式，做一些事情，比如当你的防火墙不断断开连接时保持连接，或者通过远程机器安全地隧道化网络服务。但是事情是这样的。每次我们从远程 SSH 服务器退出/注销时，我们都会丢失会话，同时丢失正在运行的每个程序。我们不希望这样。
也就是说，我们可能希望一个打开的程序，比如一个 IRC 客户端，一个爬虫脚本，任何东西，即使我们不去管它，也能继续运行，不需要我们盯着白墙绿墙的黑屏，但这就是“裸机 SSH”的不足之处，Tmux 有了闪光点——不管怎样，还是有一点。

## 满足 Tmux

tmux——实际上是“终端多路复用器”的缩写——是一种工具，它允许您在单个终端窗口中创建不同的窗口和窗格，同时还允许您持久化那些会话，以便您可以从上次停止的地方继续(您明白我在说什么吗？)

## 去肉...

### 设置 tmux

如果您已经使用过 tmux，并且对它已经很熟悉了，请随意跳到下一节。如果您还没有，这是一个相当简单的过程，让我们轻松完成它:

您很可能在您的 linux 发行版的软件包库中找到 tmux 软件包，因此安装它就像:

#### Ubuntu/Debian

`sudo apt install tmux`

#### Archlinux/Archlinux 衍生物

`sudo pacman install tmux`

#### 厘斯

`sudo yum install tmux`

现在，导航 tmux 有点复杂，虽然有鼠标支持，但掌握几个命令总是更好。好的方面是所有的命令都是可配置的，这意味着您可以映射它们以更好地适应您的工作流程。 [Hackernoon](https://dev.toHackernoon) 有一个[坚实的初学者指南在这里](https://hackernoon.com/a-gentle-introduction-to-tmux-8d784c404340)帮助你找到你的路。

## Ehem...持续会话。

首先，我们修改我们的 zsh/bash 配置文件(取决于~~你是否明白了~~你的首选 shell ),以便在没有可用会话的情况下自动创建一个新的 tmux 会话，或者如果存在的话，将它自己附加到一个现有的会话上。因此，打开配置文件，即`~/.bashrc`或`~/.zshrc`并键入以下内容:

```
if [ $(tty) == /dev/tty1 ]; then
  /usr/bin/tmux new -s 0
  /usr/bin/tmux attach -t 0
fi 
```

接下来，我们将配置文件设置为自动附加到现有的 tmux 会话(标记为“0”)，因此我们将以下内容附加到我们的`~/.profile`(如果它不存在，您必须创建它。):

`/usr/bin/tmux attach -t 0`

## 侧面吸引力:安卓版 Termux

[Termux](https://termux.com) 是一个实际工作的 Android 终端模拟器。它带来了一些好处，例如不需要 root 访问权限和一个非常加载的包存储库，以及能够在您的设备上编译您自己的包。在 termux 上使用 SSH 就像在应用程序中运行`pkg install openssh`一样简单——当然是在安装 termux 应用程序之后。安装`openssh`包也设置了您的 SSH 密钥，您可以将它们安装在您的远程 SSH 服务器上并开始运行。

现在我们可以回到我们的 SSH 服务器，但是这一次，有一个额外的冷静。

*封面图片来源:[u/authorice](https://reddit.com/user/authorice)T3】*