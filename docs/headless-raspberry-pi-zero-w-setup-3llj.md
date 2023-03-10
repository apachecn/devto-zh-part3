# Raspberry Pi Zero W 无头设置

> 原文：<https://dev.to/vorillaz/headless-raspberry-pi-zero-w-setup-3llj>

*这个教程最初是发布在我的[博客](https://www.vorillaz.com)上的。*

Raspberry Pi Zero W 是一款价格实惠且易于使用的开发板。由于它的 CPU 和 RAM 资源有限，您可以使用 Raspbian headless 模式来加速开发过程。开始时不需要额外的显示器或键盘。

## 刻录 Raspbian Lite 图像

首先:你应该从官方下载网站[页面](https://www.raspberrypi.org/downloads/raspbian/)下载 Raspbian Lite 镜像。你还必须下载 [Balena Etcher](https://www.balena.io/etcher/) 实用程序，它可以用来刷新拉斯边建兴图像，没有任何麻烦。你还需要一个微型 SD 卡，(4GB 的就很好了)。

将你的 SD 卡连接到你的电脑上，启动蚀刻机，选择 Raspbian 图像，选择 SD 卡并刷新它。这一步可能需要一段时间。当卡闪烁时，它会自动弹出。

## 启用无线联网

Raspberry Pi Zero W 有一个内置的 WiFi 芯片，可以连接到您的无线网络，前提是您首先指定凭据。为此，请将 SD 卡再次连接到电脑。蚀刻机已经将卡片重新命名为`boot`。使用您的终端作为`cd /Volumes/boot/`导航到目录。您可以随时使用 Finder 并导航到引导目录。创建一个新的空文件来存储网络信息`touch wpa_supplicant.conf`。编辑文件并输入无线网络的凭证:

```
network={
  ssid="NETWORK_NAME"
  psk="NETWORK_PASSWORD"
  key_mgmt=WPA-PSK
} 
```

Enter fullscreen mode Exit fullscreen mode

通常，这种设置是很好的。如果你发现自己有麻烦，尽管你可以看一下关于 wpa_supplicant 设置的官方文档。

## 启用 SSH 访问

现在您应该启用对您的板的 SSH 访问。假设您已经在`/Volumes/boot`目录中，创建一个空的`ssh`文件作为:`touch ssh`。

## 连接到您的板卡

现在是时候启动树莓派零。将 SD 卡插入 Pi Zero W 并连接电源；将微型 USB 电源线插入电源端口即可。一旦它通电，需要一些时间来启动和连接到 WiFi，通常从 45 到 90 秒。使用`nmap`或像 [LanScan](https://itunes.apple.com/us/app/lanscan/id472226235) 这样的网络扫描仪，你必须获得连接到网络的新设备。Pi 还将`raspberrypi`公开为主机名。使用 IP 地址你可以 SSH 你的板为:

```
~ ssh pi@192.168.1.14 
```

Enter fullscreen mode Exit fullscreen mode

输入密码，默认为*树莓*。
你也可以使用主机名进行 SSH，就像这样:

```
~ ssh-keygen -R raspberrypi.local
~ ssh pi@raspberrypi.local 
```

Enter fullscreen mode Exit fullscreen mode

## 更改您的主机名和密码

出于安全原因，最好更改主机名和密码。一旦连接到 Pi，使用`sudo raspi-config`。更改默认密码和主机名，并记下新的主机名。保存更改后，以`sudo shutdown -r now`的身份重启 Pi。假设您的新主机名是* *blueberrypi* ，您可以使用 SSH 作为`ssh pi@mypi.local`连接到板。

## 获取更新

最后，只需输入:
就可以获得 Pi 的最新更新

```
~ apt-get update -y
~ apt-get upgrade -y 
```

Enter fullscreen mode Exit fullscreen mode

## 最后的想法

树莓派零是负担得起的，超级有趣的工作。在接下来的教程中，我将向您展示如何使用 Node.js 控制外部组件，如何使用连续交付管道构建和部署应用程序等等。敬请期待！

## 故障排除

*   wpa_supplicant.conf 可能需要一些额外的设置，你可以看看官方的 Pi [文档](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)。
*   还有一些关于使用 SSH 连接到电路板的附加[文档](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md#3-enable-ssh-on-a-headless-raspberry-pi-add-file-to-sd-card-on-another-machine)。

## 购买树莓派零 W

你可以从 [Seeed Studio](https://www.seeedstudio.com/Raspberry-Pi-Zero-W-p-4257.html) 在线购买一个 Raspberry Pi Zero W 板。