# 树莓 Pi 3 Raspbian 底漆

> 原文：<https://dev.to/jeikabu/raspberry-pi-3-rasbian-primer-i6d>

这一天已经到来。

在我工作的桌子上有一个带触摸屏的树莓 Pi 3 B 运行 Windows 10 物联网核心已经有一段时间了。

我们一直用 [docker 和 Qemu 来测试 aarch64/ARM64](//./migrating-to-dockerqemu-2af6) ，但是[。ARM32 的 NET Core 在 Qemu](https://github.com/dotnet/coreclr/issues/6298) 上不工作。所以我们需要一个运行 Debian 的真实设备。刚好我又有一个圆周率 3！

## 安装 Rasbian

官方安装说明很好:

1.  [下载 Raspbian](https://www.raspberrypi.org/downloads/)
2.  [安装](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)

比如在 macOS 上:

```
diskutil list
# Note my SD card reader is /dev/disk2

diskutil unmountDisk /dev/disk2
sudo dd bs=1m if=~/Downloads/2018-11-13-raspbian-stretch.img of=/dev/rdisk2 conv=sync
sudo diskutil eject /dev/rdisk2 
```

Enter fullscreen mode Exit fullscreen mode

## 宋承宪和 VNC

我做的第一件事就是让 SSH 工作起来，这样我就可以使用一个键盘/鼠标/屏幕等等。当你在那里的时候，你可以打开 VNC

```
sudo raspi-config 
```

Enter fullscreen mode Exit fullscreen mode

*   *接口选项>宋承宪>是*
*   *接口选项> VNC >是*

当谈到 Linux 配置时，您可以进行的最佳投资之一是设置“SSH 基于密钥的认证”。如果这对你来说毫无意义，那就去研究一下，它会改变你的生活。互联网上有许多优秀的指南，但是如果你在 Linux/macOS 上，你应该能够:

```
# Replace with IP address of your Pi
ssh-copy-id pi@192.168.X.Y
# From now on you can ssh in without a password
ssh pi@192.168.X.Y 
```

Enter fullscreen mode Exit fullscreen mode

说到投资，考虑到屏幕空间的溢价，不要忽视 ssh 的`-X`选项(在 macOS/OSX 上，你还需要 [XQuartz](https://www.xquartz.org/) ):

[![](img/2da93f96196db055b74a183113bca0e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TT4yC93S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/ssh_x_pi.png)

VNC 将让您访问整个桌面(或者，查看 xrdp)。

如果您使用 RealVNC 以外的 VNC 客户端，您可能无法连接。TigerVNC 抱怨:`No matching security types`。要么使用 RealVNC，要么遵循 [VNC 配置指令](https://www.raspberrypi.org/documentation/remote-access/vnc/)。

另一个值得考虑的选项是 xrdp。在以下情况下是有意义的:

*   网络连接/带宽不是问题(以太网效果最好，VNC 也是如此)
*   您已经在使用 RDP 连接到 Windows 计算机
*   有一个小屏幕连接到 Pi

```
# On the pi
sudo apt-get install -y xrdp 
```

Enter fullscreen mode Exit fullscreen mode

然后你应该能够使用 RDP 客户端(例如[微软远程桌面 10](https://itunes.apple.com/us/app/microsoft-remote-desktop-10/id1295203466) )连接到`raspberrypi.local`或者 Pi 的 IP 地址。

## 屏幕

如果你有某种小型显示器(如 [7 英寸触摸屏](https://www.raspberrypi.org/products/raspberry-pi-touch-display/))，你可能需要额外的配置。

*   将屏幕旋转 180 度。在`/boot/config.txt`中添加:

```
 lcd_rotate=2 
```

Enter fullscreen mode Exit fullscreen mode

*   调整屏幕亮度(最大亮度为`255`，高于导致“I/O 错误”的亮度):

```
 sudo sh -c "echo 80 > /sys/class/backlight/rpi_backlight/brightness" 
```

Enter fullscreen mode Exit fullscreen mode

*   虚拟键盘。不管你称它为“软键盘”、“屏幕键盘”还是别的什么，你可能都想要一个。不管你更喜欢 iOS 还是 Android，准备好失望吧:

```
sudo apt-get install matchbox-keyboard 
```

Enter fullscreen mode Exit fullscreen mode

## 软件

更新固件(最近的设备可能不需要):

```
sudo rpi-update 
```

Enter fullscreen mode Exit fullscreen mode

对于不区分大小写的外壳自动完成，在`~/.inputrc` :
中

```
set completion-ignore-case on 
```

Enter fullscreen mode Exit fullscreen mode

几乎你要做的第一件事就是:

```
sudo apt-get update
sudo apt-get install -y vim screen # Other stuff... 
```

Enter fullscreen mode Exit fullscreen mode

如果你还没有，花点时间学习 vim 或 emacs。Nano/pico 是 Windows 记事本的 Linux 道德等价物。这需要一个月的时间来感觉富有成效，但完全值得。如果你使用 emacs 并且有一个 gimpy ctrl 键(像 Mac 笔记本电脑)，你可能想要交换“caps lock”和 ctrl:

*   MacOS: **系统偏好>键盘>键盘>修饰键……**
*   Win10:在 PowerShell([from this SO](https://superuser.com/questions/949385/map-capslock-to-control-in-windows-10)-[你用的是 PowerShell 吧？](//./powershell-crash-course-3go5)):

```
$hexified  =  "00,00,00,00,00,00,00,00,02,00,00,00,1d,00,3a,00,00,00,00,00".Split(',')  |  %  {  "0x$_"}  $kbLayout  =  'HKLM:\System\CurrentControlSet\Control\Keyboard Layout'  New-ItemProperty  -Path  $kbLayout  -Name  "Scancode Map"  -PropertyType  Binary  -Value  ([byte[]]$hexified) 
```

Enter fullscreen mode Exit fullscreen mode

因为在低端设备上，一切都需要更长的时间，如果你打算把 Pi 插在某个地方，使用笔记本电脑，“屏幕”就很方便。它允许你在闭嘴后继续会话:

```
screen # Start a session
screen -S <name> # Start session with <name>
# Ctrl-a d # Detach from the session
screen -x # Attach to running session
screen -r <name> # Attach to session with <name>
screen -dRR # Attach to session creating/detaching as needed (if multiple use first)
screen -ls # List sessions
screen -e xy # Change command character
# For example, since Ctrl-a interferes with bash's default emacs mode:
screen -e^gg
# Now `Ctrl-g d` detaches and `Ctrl-g g` is a literal `Ctrl-g` 
```

Enter fullscreen mode Exit fullscreen mode

更多美食:

*   没有官方的 VS 代码发布，但是有[社区提供的二进制文件](https://github.com/headmelted/codebuilds/releases)
*   [PowerShell 内核](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6#raspbian)
*   通过 rustup 安装 Rust:`curl https://sh.rustup.rs -sSf | sh`
*   。臂网芯( [2.2.2](https://github.com/dotnet/core/blob/master/release-notes/2.2/2.2.2/2.2.2-download.md) 和/或 [3.0.0](https://github.com/dotnet/core/blob/master/release-notes/3.0/preview/3.0.0-preview3-download.md)