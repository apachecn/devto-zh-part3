# Raspberry Pi 和 Raspbian 设置越快越好

> 原文：<https://dev.to/samuelbeard/raspberry-pi--raspbian-setup-as-fast-as-possible-39i6>

这是一个简单的安装指南，可以将 Raspbian 安装到树莓 Pi 上，并使其无需抬头即可安装。这意味着我们将不再需要插入键盘、鼠标或屏幕。

这是 MacOS 和 Linux 的教程，因为它使用了终端。如果有需求，我会写一个 Windows 特定版本。

## 确保你拥有这些东西:

*   你的树莓派
*   一种给你的树莓派提供能量的方法
*   微型 SD 卡
*   一个微型 SD 卡适配器，这样你就可以把它插入你的电脑
*   一台电脑
*   如果您的 Pi 没有内置 WiFi，您需要一个 WiFi 加密狗。

> Wifi 内置于`Raspberry Pi Zero W`和`Raspberry Pi 3`等等。

## 下载这些东西:

*   蚀刻机-[https://www.balena.io/etcher/](https://www.balena.io/etcher/)
*   拉斯比恩拉伸建兴-[https://www.raspberrypi.org/downloads/raspbian/](https://www.raspberrypi.org/downloads/raspbian/)

假设您已经安装并下载了所有的东西，让我们继续学习教程。

## 安装拉斯边

1.  解压缩您下载的 Rasbian zip 文件夹。里面应该有一个`.img`文件。
2.  使用 USB 适配器，将 micro SD 卡插入电脑。
3.  打开你电脑上的蚀刻机。
4.  在左侧部分选择`.img` Rasbian 文件。
5.  选择中间部分的 micro SD 卡。
6.  点击“闪光”并等待。

一旦完成，Rasbian 就安装好了。

默认情况下，Rasbian 不允许 ssh 连接。因此，我们需要让它们发挥作用。此外，我们希望 Pi 能够立即连接到我们的 WiFi，这样我们就不需要连接屏幕和键盘了。

## 通过 Wifi 启用 SSH

*   如果 micro SD 卡已从您的计算机上卸载，请重新连接它。
*   在终端中，转到 SD 卡。

```
# Mac
cd /Volume/MySDCard/
# Linux (Can vary or require additional steps)
cd /media/MySDCard/ 
```

Enter fullscreen mode Exit fullscreen mode

*   在 micro SD 卡中添加一个简称为`ssh`的空白文件。

```
$ touch ssh 
```

Enter fullscreen mode Exit fullscreen mode

*   在 micro SD 卡中添加一个名为`wpa_supplicant.conf`的文件。

```
nano wpa_supplicant.conf 
```

Enter fullscreen mode Exit fullscreen mode

此文件需要包含您的 WiFi 名称和密码。这是一个您可以编辑的模板。

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
network={
    ssid="YOUR_NETWORK_NAME"
    psk="YOUR_PASSWORD"
    key_mgmt=WPA-PSK
} 
```

Enter fullscreen mode Exit fullscreen mode

*   一旦这两个文件在你的 SD 卡上，就把它取出来，插到你的 Raspberry Pi 上。
*   如果 PI 需要 Wifi 适配器，请将其插入。然后给 Pi 加电。

您需要插入 Pi 的唯一电缆是电源线。

## 找到你的 Pi 的 IP 地址

你可以通过查看你的路由器用户界面来做到这一点。只要找到那个叫‘树莓派’的装置就行了。

如果要在终端中查找 IP:

```
arp -na | grep -i b8:27:eb 
```

Enter fullscreen mode Exit fullscreen mode

这会给你一个以 192.168 开头的 IP 地址。那是你的 Pi 的内部 IP 地址。您只能使用此地址从同一网络上的另一台设备访问您的 Pi。

所有 Raspberry Pi 设备的 MAC 地址都以“B8:27:EB”开头。

## 访问和设置

*   嘘到你的 Pi 上

```
sh pi@192.168.x.x 
```

Enter fullscreen mode Exit fullscreen mode

用您的 Pis IP 地址替换 Xs。

*   当要求输入密码时，键入“raspberry”。看起来好像什么都没有输入。别担心，是这样的。

*   这里我们要做的第一件事是更改默认密码。

```
passwd 
```

Enter fullscreen mode Exit fullscreen mode

*   更新

```
sudo apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

您已经准备就绪，可以开始了！从现在起，登录 Pi 时只需使用您的新密码。

* * *

如果你有问题，尽管问。

您还可以查看[树莓 Pi 堆栈交换](https://raspberrypi.stackexchange.com/)