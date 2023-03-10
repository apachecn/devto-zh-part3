# 回收您的旧笔记本电脑来升级您的树莓派设置！

> 原文：<https://dev.to/schmowser/recycle-your-old-laptop-to-upgrade-your-raspberry-pi-setup-54c5>

## 配料

*   旧笔记本
*   带 SD 卡和电源的树莓派
*   以太网电缆

几周前，我的一个朋友给自己买了一个新笔记本，她想知道现在她能用旧的做什么。因为她不想把它扔进垃圾桶，所以她转向了我。她问我是否有新用途的建议。

## 文章不再可用

然后，我想起我最近设置了一个 Raspberry Pi 作为开发服务器，灵感来自上面的 *dev.to* 帖子。但由于我不想为树莓派单独配备显示器、键盘和鼠标，所以我想到了如何重复使用她的旧笔记本。使用笔记本电脑进行互动！因此，这将是一个精简版的 [*可指导*的](https://www.instructables.com/id/How-to-connect-raspberry-pi-to-laptop-display/)将覆盆子馅饼与笔记本电脑连接起来。但我还是希望能有所帮助。

## 设置 Pi

所以，我有 Pi 型号 3B+在这里，幸运的是船上有一个无线局域网适配器！我用 [Raspbian](https://www.raspberrypi.org/downloads/raspbian/) 闪了一下 SD 卡。不知何故，没有一个 Ubuntu 发行版能和我一起工作，因为它们不能识别 wlan 特性。但是除此之外，Sarthak Sharma 的文章是一个让你的 Pi 运转起来的很好的介绍。不幸的是，在完成以下步骤之前，您需要一个屏幕和某种键盘来输入这些指令。但希望这是最后一次。😉

最重要的是，你得知道你的树莓派的 IP 地址。你可以通过打开终端并输入
很容易地得到它

```
hostname -I 
```

Enter fullscreen mode Exit fullscreen mode

地址一般是这样的形式: *xxx.xxx.xxx.xx* 。把它记下来，你以后会需要的。现在，安装 TightVNC——这是一个远程控制软件包。而且是免费的！这样，我们将启动一个远程服务器，并从笔记本电脑上访问它。

```
sudo apt-get update
sudo apt-get install tightvncserver 
```

Enter fullscreen mode Exit fullscreen mode

安装后，启动一个服务器并指定一个端口(这里是:1)。

```
sudo vncserver :1 
```

Enter fullscreen mode Exit fullscreen mode

您将被要求设置密码。出于某种原因，你必须选择一个 8 个字符的密码。任何更长的都将被截断。😅

## 设置笔记本电脑

抓住旧的东西，启动它，把以太网电缆塞进去！另一端用于连接覆盆子。为了连接到您刚刚创建的 VNC 服务器，您需要使用 [VNC 浏览器](https://www.realvnc.com/de/connect/download/viewer/)。一次下载和一次安装后，你应该能够创建一个新的连接，你指定的主机名和端口上面获得:

```
xxx.xxx.xxx.xx:1 
```

Enter fullscreen mode Exit fullscreen mode

*加密*可以留在*让 VNC 服务器选择*。请确保正确输入您的 8 位数字密码，然后按回车键！你应该看看你的树莓派会呈现什么。VNC 检视器通常会存储设置，以便您下次需要新笔记本电脑的屏幕、键盘和触控板时可以轻松地重新连接。

这就是你如何从垃圾桶里救出一台旧笔记本电脑的方法！