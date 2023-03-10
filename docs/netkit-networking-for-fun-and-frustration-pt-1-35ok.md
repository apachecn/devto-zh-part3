# 网络工具包-网络的乐趣和挫折。一

> 原文：<https://dev.to/luconit/netkit-networking-for-fun-and-frustration-pt-1-35ok>

所以你可能会考虑测试这个新的管理工具，但是你的办公室、家里或房间里没有三台(工作的)电脑。或者你有了如何建立新网络的想法，并想测试一些配置？或者也许你的教授给了你一个网络任务，你必须建立一个网络设置，但你真的没有所有的硬件？是的，我感觉到了，但是我有一个很好的软件给你。网络工具包。本文试图向您简要介绍使用 Netkit 进行联网。复杂的东西以后再说，抱歉。

## 什么是 Netkit？

Netkit 基本上是一个与 linux 主机进行虚拟联网的程序，无需购买一些昂贵的硬件。最初的 netkit 的最后一个版本是 2016 年的一个不稳定版本，但它由另一个人开发，因为它是开源的。所以有一些其他和更新的版本:有 [Netkit-ng](https://netkit-ng.github.io/) (我最喜欢的)，还有 [Kathará](http://www.kathara.org/) (与 docker 一起工作)，如果你搜索足够多，还有更多。Netkit 和 Netkit-NG 基于 debian-virtual machines 和 user-mode-linux，这使得 linux 内核能够在用户空间中运行(如果我说错了，请纠正我)。你也可以从网站上下载很多所谓的实验，这些是预先配置的机器和机器配置的组合。您也可以自己建立一个实验室，并将其上传到网络上(我们将在下一篇文章中对此进行探讨)。\
很抱歉，它只能在基于 Linux 的操作系统上运行。

## 那怎么办呢

如果您想测试您的新网络配置、路由表，或者测试另一个配置管理工具，如 SALT 或 Ansible，或者最终使用 puppet，但您只有一台机器或最多两台机器，但不足以真正运行测试。或者你学习计算机科学或计算机科学中的某个领域，你的教授希望你制定一个网络计划并进行测试。或者你想教一些简单的网络，而不用花很多钱买合适的硬件。

## 怎么用？

在这篇文章中，我将只告诉你如何安装 netkit 和启动你的第一台机器，在此之前，请阅读本系列的下一篇文章。

### 下载文件并配置就绪

我最喜欢的是 Netkit-NG，因为它相当新，而且一些基本功能正在工作，所以你必须做的是下载程序的核心、内核和文件系统。核心是命令，内核是使用的 linux 内核，文件系统是你的虚拟机基于的 debian 文件系统。\
然后你必须把它插入一个文件夹，用命令解压，其中“版本”是你已经下载的版本:

```
tar -xjSf netkit-ng-core-32-version.tar.bz2
tar -xjSf netkit-ng-filesystem-i386-version.tar.bz2
tar -xjSf netkit-ng-kernel-i386-version.tar.bz2 
```

您已经下载了文件并解压了它们，现在您必须将路径添加到您的 PATH 中，所以打开您的。bashrc(或者。bash_profile)(该文件位于您的 home 文件夹中)并添加变量 NETKIT_HOME 以及您的 netkit-files 的路径，还将$NETKIT_HOME/man 添加到您的 MANPATH 中，并在您的路径中包含$NETKIT_HOME/bin。在我的。巴沙尔看起来是这样的:

```
export NETKIT_HOME=/home/luca/programs/netkit-ng
export MANPATH=:$NETKIT_HOME/man
export PATH=$NETKIT_HOME/bin:$PATH 
```

由于 Netkit 是 32 位开发的，我们必须获得一些依赖项，所以必须安装这三个包:libc6:i386、libncurses5:i386 和 libreadline6:i386。您还需要一个终端，Netkit 主要使用它们，但不是任何终端，您需要 xTerm。现在，你终于可以开始了。好吧，让我们在开始之前测试我们的配置，因为安全比烦恼好。因此，让我们导航到我们的 netkit 文件夹并运行 check-configuration.sh。如果一切正常，我们可以开始了。

### 启动第一台虚拟机

完成这些配置、安装等工作后，让我们启动我们的第一个虚拟机，并在其中获得一些不错的网络和软件包更新。

要轻松启动一个单独的虚拟机，只需键入`vstart pc1`，其中 pc1 只是名称。但是没有人应该是孤独的，没有互联网的连接，所以让我们用`vhalt pc1`结束这个 vm 来再次启动它，但是这次我们想要互联网连接，所以让我们键入`vstart pc1 --eth0=tap,10.0.0.1,10.0.0.2`，现在我们有一个 tap 接口。
ip 地址不应在您的子网中(IP 地址中的前三个数字不应相同),但必须在同一子网中。第一个称为 tap-address，第二个称为 guest-address。好了，现在我们有了一些可爱的、漂亮的、新鲜的互联网，但是仅仅运行一些 apt-get-commands 并不能真正工作，我们需要空间来实现它。让我们再次暂停虚拟机(`vhalt pc1`)并用这个命令`vstart vm --eth0=tap,10.0.0.1,10.0.0.2 -M 256`添加一些简单的 RAM。现在你可以用`apt-get`命令更新虚拟机上的包，如果你想永久保存你更新和安装的应用程序，只需在下次启动虚拟机时在命令中添加 a -W。

#### 子网？这是什么？

子网就像较小的网络，我们将在下一篇文章中更深入地探讨这一点，在下一篇文章中，我们将开始创建自己的子网，但基本上它只是一个更大的计算机网络中的一个计算机网络。在本例中，子网 I 指的是第三个数字(10.0.XXX.1 中的 x)。更多信息你可以阅读维基百科的文章，它对我帮助很大:[https://en.wikipedia.org/wiki/Subnetwork](https://en.wikipedia.org/wiki/Subnetwork)

#### 也这是什么 apt-get？

Apt-get 是基于 debian 发行版的终端包管理前端。简单来说:基于 debian 的发行版都有管理已安装程序的包管理工具 dpkg，像 apt-get、apt 和 aptitude(以及更多)这样的工具可以帮助你解决依赖性，帮助你通过终端从你的机器上安装、更新和删除程序。一些重要的命令是:`sudo apt-get install <package>`安装软件包，sudo apt-get update 更新你的软件包数据库(每次你想安装一个软件包或更新软件包时，你必须这样做)，sudo apt-get upgrade 升级你所有的软件包和`sudo apt-get remove <package>`删除所说的软件包。

行...今天的内容已经足够了，下次我将向您介绍 netkit labs，让您的孤独机器不再那么孤独。如果你有任何问题，请写信给我，我很乐意尽我所能帮忙。此外，如果你有建议和建设性的批评，请让我知道。