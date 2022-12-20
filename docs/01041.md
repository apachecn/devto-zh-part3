# 肌球蛋白

> 原文：<https://dev.to/taillogs/myos-3kdm>

[Github](https://github.com/rylandg/myos)

TLDR；使用 Docker 作为 *lite* VM 来实现一个可移植的、可共享的开发环境

##### 灵感

在我成为 Binaris 的产品负责人之前，我是我们横向扩展后端计算产品的首席架构师。正如你所料，这涉及到大量的*隐藏* `<insert sunny reference here>`，以及远程调试和开发。我很快厌倦了在每个节点上从头开始重新创建我的环境。

为什么不是点文件？

点文件解决了这个问题的一部分，但是点文件通常不处理包安装和其他样板系统配置。包含脚本很好，但最多是不稳定的。脚本也只有在假设底层操作系统不变的情况下才有用。对于那些想知道我为什么不简单地烘焙一个 AMI 的人来说，每个 AMI 都是针对其上运行的微服务而优化的，而不是我的一次性开发需求。我的配置最终包括了点文件、shell 脚本和 Ansible 剧本，只是为了创建一个一致的环境。

##### 盗梦

几个月前，我开始好奇是否有可能/流行使用 Docker 作为 *lite* VM。我在网上看到很多人在 docker 中运行远程服务器(特别是 VSCode ),但是很少有人真正在 docker 容器本身内部开发*。我们在工作中经常使用 Docker，所以我已经非常熟悉它的工具和功能。因为我想重做我的 dotfiles+config 已经有一段时间了，所以我想为什么不用 Docker。*

##### 肌球蛋白

通过我的努力得到的 Dockerfile 文件现在已经成为了 MyOS 的基础。Dockerfile 为使用 vi、zsh 和 tmux 的容器内开发提供了半自以为是的基础。它处理许多样板文件，比如

*   配置区域设置和颜色
*   创建非根用户并设置必要的权限
*   为无密码登录设置 OpenSSH
*   启用 X11 显示服务器

最初，我使用 vanilla Ubuntu 作为基础映像，但为了获得我想要的基本功能，它需要太多的修改。最终，我发现了 [Phusion](https://github.com/phusion/baseimage-docker) ，这是一个了不起的项目，提供了一些关键特性

*   超轻，高度优化的基本 Ubuntu 图像
*   “安全”运行多个流程的机制
*   初始化以 PID > 1 运行您的用户进程
*   开箱即用的 OpenSSH 服务器

我还包含了一些固执己见的基线包。有些是显而易见的，如用于本地主机复制/粘贴的`xauth`。其他人我也包括在内，因为正确设置比运行`apt-get`需要更多的时间。亮点是

*   ZSH
*   快上来
*   带剪贴板支持的 Vim8
*   从源代码构建的最新 Tmux
*   用于剪贴板支持的扩展验证和扩展显示包

##### 使用肌球蛋白

肌球蛋白很少。今天有三个主要部分

1.  [Dockerfile](https://github.com/rylandg/myos/blob/master/Dockerfile)

定义基本的 MyOS 图像。处理内置软件包安装和系统配置。

1.  [码头-化合物. yml](https://github.com/rylandg/myos/blob/master/docker-compose.yml)

控制将哪些主机卷和端口映射到容器中。这个文件很关键，因为它允许我们将编辑器(vi)、shell (zsh)和窗口管理(tmux)配置文件挂载到容器中，而不需要实际将它们存储在容器中。

1.  [index.js (CLI)](https://github.com/rylandg/myos/blob/master/index.js)

我需要一种便捷的方式反复运行命令。最初我混合使用 sh 和 Make，但总是讨厌维护用这些工具编写的 CLI。目前，它是基于节点的，允许用 npm 轻松安装(我肯定愿意改变这一点)

要使用 MyOS，首先全局安装 CLI

```
$ npm install -g myos 
```

接下来，您需要克隆一个现有的 MyOS 设置，或者创建一个空模板。

```
$ myos init myos-config
$ cd myos-config
$ ls docker-compose.yml tmux vim zsh 
```

一旦你进入一个包含有效的 MyOS 设置的目录，运行

```
$ myos create rysenv 
```

从今天开始，`create`命令实际上运行`docker-compose up -d`。

一旦容器启动，使用
连接

```
$ myos connect 
```

仅此而已。

##### 值得吗？

**免责声明:显然这是对容器模型的完全滥用**

容器肯定有一些限制，但是使用容器作为开发环境有一些显著的好处。

*   难以置信的可移植性——Docker 是开发环境的 JVM，支持本地 OSX、Windows 和 Linux
*   非常容易破坏——重新创建一个容器几乎没有开销，所以请放心使用`rm -rf *`
*   大部分是无状态的——除了最基本的，状态来自安装的 Docker 卷。这使您可以在容器环境中轻松地处理您的主机文件，而不会附加到容器上。
*   惊人的性能——在 Linux 上，期望本机内核性能，在 OSX 和 Windows 虚拟机级别的性能(OSX 上的 Docker 运行在虚拟机中)。
*   加速 env 迭代——由于它的分层文件系统，您可以对运行时环境进行大规模调整，而不必手动删除和重新安装依赖项。
*   可转移——利用`docker commit`和`docker push`以及 AWS ECR，在维护文件系统状态的同时从一个主机转移到另一个主机(但是请保持无状态)

##### 共享

虽然我主要看重 MyOS 对我个人发展的价值，但我每天都浏览 [/r/vim](https://www.reddit.com/r/vim/) ，subreddit 上的互动让我想知道 MyOS 是否可以用于更多用途。MyOS 可以促进整个终端环境的共享，而不仅仅是点文件。分享你的设置，并且知道它保证可以在任何系统上为任何人工作，这不是很酷吗？假设每个人都依赖于一个半稳定的基础图像，共享要求您只发送您已经添加到基础图像之上的图层以及您的点文件和其他配置。

##### 最后备注

我把我所有的作品都开源了，如果其他人也能使用我的作品，我会很高兴。我很乐意讨论功能，甚至是基于社区需求的变化，拉请求肯定是受欢迎的。有几项我已经知道是有问题的

*   非常关注 vim，想看看是否有可能支持其他编辑器(肯定是 Emacs)
*   需要允许您复制另一个用户的 MyOS 环境的 CLI 命令
*   多个 MyOS 环境应该能够同时运行
*   authorized_keys 的 SSH 密钥可以自动生成
*   可能会从 CLI 的 NodeJS 中移走