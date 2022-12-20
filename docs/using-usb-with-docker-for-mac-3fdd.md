# 将 USB 与 Docker for Mac 配合使用

> 原文：<https://dev.to/rubberduck/using-usb-with-docker-for-mac-3fdd>

如果你一直在这个问题上挣扎，只是想要一个答案，跳到底部的 TL；博士。我不会为此责备你。

Docker 是部署 web 服务的一个很好的工具，但是我最喜欢用它来标准化工具链。团队中的每个人都需要设置相同的开发环境，然后保持它们的同步，而不是在一个地方定义构建工具，一个 docker 映像。然后每个人，包括您的构建服务器，都使用这个单独的预打包映像。

这不仅对团队来说很棒，而且当你有一些只是定期回顾的附带项目时，这也很棒。毫无疑问，当我回到某个老项目时，我已经更新了我的工具，如果不升级项目或者降低我的工具，我就不能构建那个老项目。留下一个构建工具映像意味着我可以直接拿起它进行工作，而不用花一天的时间重新启动和运行。

然而并不全是阳光和玫瑰。我今天进行了一次冒险。去年我为 Arduino 工作室做了一个 [TDD。我已经开始为 AVR 开发](https://christopherjmcclellan.wordpress.com/?p=2103)创建[Docker 映像，但是在将程序刷新到板上时遇到了问题。将 USB 端口暴露给 Mac 上的 docker 容器并不是一项简单的任务(至少在您知道如何做之前！).对于那次会议，我们群起而攻之，所以我只需要设置一台机器。我只是停止了与它的斗争，并在我的机器上安装了一个常规的工具。](https://hub.docker.com/r/rubberduck/avr/)

不过最近，我对让它正常工作产生了新的兴趣。首先，我一直在玩 ARM 开发，但是[AVR 和 ARM 的编译器打包有一个 bug，意味着你不能同时安装两个工具链](https://bugs.launchpad.net/ubuntu/+source/gcc-avr/+bug/1746955)。将这些工具链集装箱化意味着我可以很容易地随时使用它们。其次，我现在开始在这个研讨会的基础上，把它变成一个“裸机编程入门”课程。为此，我真的需要能够给人们一个我知道可以工作的环境，这样我们就不会花更多的时间来解决开发设置中的问题，而不是学习。此外，为了给工作中的团队或客户标准化嵌入式工具链，我真的需要知道如何让 USB 在 Mac 上工作。

如果您运行的是 Linux，这就像在 docker run 命令中添加`--device /dev/ttyUSBx`一样简单。不用说，这在 OSX 或 Windows 上并不简单。这是因为 docker 守护进程只在 Linux 上本地运行。对于其他操作系统，它运行在管理程序或虚拟机中。为了将端口公开给容器，首先必须将其公开给运行 Docker 的虚拟机。不幸的是， [hyperkit，Docker-For-Mac 使用的管理程序不支持 USB 转发](https://github.com/docker/for-mac/issues/900)。

由于我们不能将 USB 端口暴露给本机 Mac Docker 虚拟机管理程序，所以我们必须退回到`docker-machine`，它使用 Virtualbox VM 来托管`dockerd`守护进程。有[很好的说明来设置带有 USB 过滤器的 docker-machine](http://gw.tnode.com/docker/docker-machine-with-usb-support-on-windows-macos/)，但是我从`docker-machine`那里得到了很多神秘的 segfaults，它会让我的 VM 运行，但是也无法通过 docker-machine 恢复到它的连接。原来几个版本的 VirtualBox 都有一个导致 segfaults 的 bug。升级到 v6.06 解决了那个问题，但是我还是看不到设备。我花了太长时间才想起几个月前我在 Windows 来宾操作系统上使用 USB 3.0 时遇到了同样的问题。下降到 USB 2.0 解决了这个问题。

好了，让我们言归正传，在 Mac 上运行一个完全容器化的嵌入式工具链。

首先，[下载安装 VirtualBox 版或更高版本](https://www.virtualbox.org/wiki/Downloads)。同样，这必须是版本 6.06 或更高版本，否则当您稍后尝试创建 USB 过滤器时会看到 segfaults。你可以选择安装[VirtualBox 扩展包](https://download.virtualbox.org/virtualbox/6.0.6/Oracle_VM_VirtualBox_Extension_Pack-6.0.6.vbox-extpack)。尽管如此，我还是推荐它，因为它支持 USB 2.0，从而加快了编程速度。

接下来，我们可以创建并设置我们的`docker-machine`(虚拟机)。

```
#! /bin/bash
#create and start the machine
docker-machine create -d virtualbox default

#We must stop the machine in order to modify some settings
docker-machine stop

#Enable USB
vboxmanage modifyvm default --usb on

# OR, if you installed the extension pack, use USB 2.0
vboxmanage modifyvm default --usbehci on

# Go ahead and start the VM back up
docker-machine start

# Official Arduinos and many clones use an FTDI chip.
# If you're using a clone that doesn't, 
# or are setting this up for some other purpose
# run this to find the vendor &amp;amp;amp;amp;amp;amp;amp;amp; product id for your device.
# vboxmanage list usbhost

# Setup a usb filter so your device automatically gets connected to the Virtualbox VM.
vboxmanage usbfilter add 0 --target default --name ftdi --vendorid 0x0403 --productid 0x6015

#setup your terminal to use your new docker-machine
#(you must do this every time you want to use this docker-machine or add it to your bash profile)
eval $(docker-machine env default) 
```

现在，插入您的设备并运行这个命令来验证容器可以看到它。

```
docker run --rm -it --device /dev/ttyUSB0 ubuntu:18.04 bash -c "ls /dev/ttyUSB0" 
```

如果该命令失败，请确保您的设备已接通电源，并且对虚拟机可见。您可能键入了错误的供应商和产品 id，或者 tty 可能附加在不同的号码下。

```
docker-machine ssh default "ls /dev/tty*" 
```

就是这样。就像我说的，一旦你知道怎么做就很容易了。不幸的是，没有官方文档，考虑到`docker-machine`和`boot2docker`都处于维护模式，我希望我们将来能在 hyperkit 上获得对 USB 的官方支持。容器化构建工具是团队利用该技术的一个很好的方式，即使您不使用它们来部署服务。现在，如果你不介意的话，我需要用一个脚本来更新我的 AVR toochain 图像，并添加`avrdude`来上传程序。

下次再见，
Semper Cogitet