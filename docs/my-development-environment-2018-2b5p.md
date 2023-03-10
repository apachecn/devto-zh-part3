# 我的开发环境 2018

> 原文：<https://dev.to/risdenk/my-development-environment-2018-2b5p>

### 概述

有一天，有人问我我的开发环境是什么样的，因为我能够快速测试许多不同的配置。我写这篇文章是为了捕捉我所做的一些东西，以便能够快速迭代。首先介绍一些背景知识，为什么快速改变测试环境对我来说一直很重要。

#### 背景

我之前在 [Avalon Consulting，LLC](https://www.avalonconsult.com/) 担任软件顾问。我们为许多不同的客户做过各种各样的项目。有些项目时间长，有些项目时间短。我主要关注大数据和搜索。[具有安全性的 Apache Hadoop](https://hadoop.apache.org/) 有很多不同的配置。为每个小测试设置云环境是不实际的(酒店的 wifi 太差了)。这意味着我需要找到一种方法来测试我的 8GB Macbook Pro。

### 开发笔记本电脑

我目前有 2 台笔记本电脑用于开发。一台 2012 年的 8GB 内存 Macbook Pro 开始显示出它的年龄，但它值得每一分钱。第二个工作笔记本电脑，我不会进入太多的细节。两台笔记本电脑的配置非常相似。关键软件包括:

*   [iTerm2](https://www.iterm2.com/)
*   [自制](https://brew.sh/)
*   [Zsh](http://www.zsh.org/)
*   哦-我-zsh
*   [去](https://git-scm.com/)
*   [MAC 坞站](https://docs.docker.com/docker-for-mac/)
*   [虚拟盒](https://www.virtualbox.org/)
*   [流浪者](https://www.vagrantup.com/)
*   [IntelliJ IDEA 终极版](https://www.jetbrains.com/idea/)
*   [铬合金](https://www.google.com/chrome/)

我经常使用我的终端。我用它做 git，ssh，docker，流浪者等。我通常让我的终端一直开着，因为我通常在运行一些东西。我经常在 Docker 和流浪者/Virtualbox 之间切换。有许多安全和分布式计算设置，在这些设置中，正确的主机名和 DNS 解析可以更好地处理完整的虚拟机。如果你知道你是在和“真正的”机器打交道，而不是和 Docker 网络和 DNS 打交道，那么你会遇到更少的麻烦。

我应该感谢 Travis CI，因为我在我的开源项目中经常使用它们。我通常将一个 git 分支推到我的 Github fork，让 Travis CI 去工作。这让我可以在测试需要 10 分钟的时候同时做多件事情。

### 英特尔 NUC 服务器

我最近在我的开发设置中添加了一个[英特尔 NUC](https://simplynuc.com/8i5beh-kit/) ,以帮助我从笔记本电脑上卸载一些长时间运行的测试。它还具有更多的 RAM 和 CPU 能力，允许我运行连续的集成作业以及更多的流浪虚拟机。我在英特尔 NUC 上运行的一些软件(主要作为 Docker 容器):

*   [Dnsmasq](https://en.wikipedia.org/wiki/Dnsmasq)
*   詹金斯
*   [Nexus 3](https://www.sonatype.com/download-oss-sonatype)
*   [Gogs](https://gogs.io/)
*   [声纳曲](https://www.sonarqube.org/)

Dnsmasq 确保我可以在我的 NUC 和我的私有网络中获得一致的 DNS。Jenkins 运行我的大部分持续集成构建。它有助于跟踪日志，并允许我为不同的目的加速工作(比如重复测试一个特性分支)。Jenkins 为每个构建创建了单独的 Docker 容器，所以我不必担心依赖冲突。Nexus 允许我缓存 Maven 存储库、Docker 图像、静态文件等等。这确保了我不需要等待一遍又一遍地重新下载相同的依赖项。Gogs 是一个独立的 Git 服务器，让我可以轻松地在内部镜像 repos。这避免了我不得不一遍又一遍地从网上拉大回购。Sonarqube 使我能够针对一些 Jenkins 构建运行一些额外的静态构建检查。

### Yubikey

我想谈一谈我使用 Yubikey 的情况。几年来我一直想买一个，终于在 Yubikey 5 出来的时候买了一个。现在 GPG 和宋承宪都在用它。我可以不在我的新设备上存储任何私钥，甚至可以在必要时从 chrome book SSH 回我的服务器。我配置了我的 Yubikey 来处理签名和认证的 GPG。这也允许我在 SSH 中使用 GPG。GPG 代理需要一点配置，但是一旦设置好，您就可以轻松地将它用于 GPG 和 SSH。我希望更多的网站支持 U2F，而不是誓言/认证代码。我喜欢这种简单性，并向大多数开发人员推荐它。

### 结论

在过去的 5 年里，当谈到开发笔记本电脑时，我的设置没有太大的变化。我已经开始使用更多的基于云的自动化测试，比如 Travis CI。我添加了英特尔 NUC，以便能够跨更大的虚拟机进行更多内部测试。我要说的是，我学到的在 8GB 内存的笔记本电脑上安装分布式系统的经验比其他任何东西都多。(还有谁能说他们已经在 3 个 Linux 虚拟机和 1 个 8GB 内存的 Windows AD 虚拟机上运行了 Hadoop)。谁知道 2019 年会发生什么，但我对 2018 年所拥有的一切感到高兴和富有成效。