# 使用 dropbear 远程解密引导驱动器

> 原文：<https://dev.to/j6s/decrypting-boot-drives-remotely-using-dropbear-2hdf>

如今，没有理由不加密你的引导盘:我甚至可以说，如果你不加密，你就是在玩忽职守。

有些时候，您无法亲自解密驱动器:例如在服务器、NAS 中，或者如果您想要远程访问您的台式机。为了输入加密密码，能够 ssh 到您的机器不是很好吗？有可能。

**注意:** Dropbear 似乎在过去几年里得到了非常积极的发展——你在网上找到的许多指南都已经过时了。本文截止到 2019 年初。

## 你需要什么

本文假设一个最新的 Debian 或 Ubuntu 系统——尽管其他系统也有类似的现成 initramfs 包。所有步骤都已经在 Debian 10 上测试过了，但是在 Ubuntu 上应该也是一样的。

## 安装`dropbear`

Dropbear 由两个组件组成:- `dropbear`是一个非常轻量级的 SSH 服务器- `dropbear-initramfs`是一个针对`dropbear` SSH 服务器的 initramfs 集成。

我已经说过 initramfs 很多次了，但没有解释它是做什么的。对于所有的意图和目的，initramfs 可以被认为是一个在操作系统之前启动的微系统，它负责一些管道工作(比如解密和安装驱动器)。

## 配置`dropbear`

使用`dropbear-initramfs`,只需要最少的配置:为了让一切正常工作，你必须做的唯一一件事就是将你的客户端设备的公钥添加到`/etc/dropbear-initramfs/authorized_keys`,并运行`sudo update-initramfs -u`来更新 initramfs 映像。

重新启动电脑时，IP 地址将打印到屏幕上。您现在可以使用`ssh root@{YOUR_IP}`连接到系统，并使用`cryptroot-unlock`来解锁您的磁盘。

## 配置静态 IP 地址

当然，查看屏幕来获取 IP 地址违背了目的——因此我们必须确保 PC 在 initramfs 中使用静态 IP 地址。该配置不同于(`/etc/network/interfaces`或通过网络管理器)中已经存在的配置，因为它必须在系统解密和启动之前存在。

编辑`/etc/initramfs-tools/initramfs.conf`并在`DEVICE=`行下添加一行。

```
IP=192.168.0.30:192.168.0.1:255.255.255.0::enp5s0 
```

Enter fullscreen mode Exit fullscreen mode

这一行的格式是`IP=ipaddress::gateway::netmask::hostname:eth`——主机名可以省略。

再次运行`sudo update-initramfs -u`来更新 initramfs 映像后，我们的 PC 将使用静态 IP 地址启动。

## 避免客户端主机密钥冲突

如果您经常使用 ssh 访问机器，您可能会注意到 ssh 警告您要更改主机密钥——这是因为 openssh 和 dropbear 是两个独立的 SSH 密钥，具有独立的主机密钥集。不建议对两者使用相同的密钥，因为 initramfs 没有加密。

为了避免主机密钥冲突，您可以在客户端的`~/.ssh/config`中配置一个单独的可信主机存储:

```
Host jo-desktop-unlock
    Hostname 192.168.0.30
    User root
    UserKnownHostsFile ~/.ssh/known_hosts.initramfs 
```

Enter fullscreen mode Exit fullscreen mode

## 额外:只允许解密

默认情况下，Dropbear 会将你放入一个 shell 中——这样做的主要缺点是你必须记住`cryptroot-unlock`命令(shell 中没有真正的帮助),这很容易出错。

幸运的是`dropbear`有办法在连接后立即运行特定的命令。要立即运行解锁命令，将以下内容添加到`/etc/dropbear-initramfs/config` :

```
DROPBEAR_OPTIONS='-c cryptroot-unlock' 
```

Enter fullscreen mode Exit fullscreen mode