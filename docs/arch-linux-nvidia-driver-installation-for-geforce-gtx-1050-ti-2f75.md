# arch Linux:GeForce GTX 1050 Ti 的 NVIDIA 驱动程序安装

> 原文：<https://dev.to/setevoy/arch-linux-nvidia-driver-installation-for-geforce-gtx-1050-ti-2f75>

[![](img/9784b61222c0d3b6a1a5320be0f28709.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bNb1hH5q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2018/04/archlinux_logo.png) 当我购买一台配备 NVIDIA GeForce GTX 1050 Ti 的新电脑时，我的主要目标是能够在 Arch Linux 上玩游戏，而不必重启进入 Windows。

为此需要安装 NVIDIA 驱动程序，因此 Linux 将使用它来代替`nouveau`(尽管我甚至没有尝试用`nouveau`运行任何游戏)。

几年前，在我尝试在笔记本电脑上安装 NVIDIA 驱动程序之后，我也预料到了这里的问题，并准备好深入研究 X.Org 配置和日志。事实上，我甚至在面临这些安装问题之前就开始写这篇文章了。

突然间，一切都变得几乎没有问题了——我面临的唯一一个问题是由我自己的错误造成的，请看下文。

这里的文献资料是[>>>](https://wiki.archlinux.org/index.php/NVIDIA)。

### NVIDIA 驱动程序安装–尝试#1(错误)

找到现在使用的显卡和驱动::

```
[setevoy@setevoy-arch-pc ~]$ lspci -k | grep -A 2 -E "(VGA|3D)"
01:00.0 VGA compatible controller: NVIDIA Corporation GP107 [GeForce GTX 1050 Ti] (rev a1)
Subsystem: ASUSTeK Computer Inc. GP107 [GeForce GTX 1050 Ti]
Kernel driver in use: nouveau 
```

查看老卡列表这里[这里> > >](https://www.nvidia.com/object/IO_32667.html) 。

实际上，唯一的问题是我被`nvidia-lts`包名中的“ *lts* 后缀所诱惑。

安装它(**否** ):

```
[setevoy@setevoy-arch-pc .config]$ sudo pacman -S nvidia-lts 
```

重启电脑，正如我所期待的那样，我正在等待问题的出现——open box 无法启动。

检查现在使用的驱动:

```
[setevoy@setevoy-arch-pc ~]$ lspci -k | grep -A 2 -E "(VGA|3D)"
01:00.0 VGA compatible controller: NVIDIA Corporation GP107 [GeForce GTX 1050 Ti] (rev a1)
Subsystem: ASUSTeK Computer Inc. GP107 [GeForce GTX 1050 Ti]
Kernel modules: nouveau 
```

> 内核模块:新

嗯…

和 X.Org 日志`/var/log/Xorg.0.log` :

```
...
[    32.079] (==) Matched nouveau as autoconfigured driver 0
[    32.079] (==) Matched nv as autoconfigured driver 1
[    32.079] (==) Matched modesetting as autoconfigured driver 2
[    32.079] (==) Matched fbdev as autoconfigured driver 3
[    32.079] (==) Matched vesa as autoconfigured driver 4
[    32.079] (==) Assigned the driver to the xf86ConfigLayout
[    32.079] (II) LoadModule: "nouveau"
[    32.107] (WW) Warning, couldn't open module nouveau
[    32.107] (EE) Failed to load module "nouveau" (module does not exist, 0)
[    32.107] (II) LoadModule: "nv"
[    32.107] (WW) Warning, couldn't open module nv
[    32.107] (EE) Failed to load module "nv" (module does not exist, 0)
[    32.107] (II) LoadModule: "modesetting"
[    32.107] (II) Loading /usr/lib/xorg/modules/drivers/modesetting\_drv.so
[    32.123] (II) Module modesetting: vendor="X.Org Foundation"
[    32.123]    compiled for 1.20.3, module version = 1.20.3
[    32.123]    Module class: X.Org Video Driver
[    32.123]    ABI class: X.Org Video Driver, version 24.0
[    32.123] (II) LoadModule: "fbdev"
[    32.123] (WW) Warning, couldn't open module fbdev
[    32.123] (EE) Failed to load module "fbdev" (module does not exist, 0)
[    32.123] (II) LoadModule: "vesa"
[    32.123] (WW) Warning, couldn't open module vesa
[    32.123] (EE) Failed to load module "vesa" (module does not exist, 0)
[    32.123] (II) modesetting: Driver for Modesetting Kernel Drivers: kms
[    32.123] (EE) open /dev/dri/card0: No such file or directory
[    32.123] (WW) Falling back to old probe method for modesetting
[    32.123] (EE) open /dev/dri/card0: No such file or directory
[    32.123] (EE) Screen 0 deleted because of no matching config section.
[    32.123] (II) UnloadModule: "modesetting"
[    32.123] (EE) Device(s) detected, but none match those in the config file.
[    32.123] (EE)
Fatal server error:
[    32.123] (EE) no screens found(EE)
... 
```

好吧…谷歌？:-)

找到了[这个> > >](https://bbs.archlinux.org/viewtopic.php?id=231722) 的讨论——我们来试试。

需要更新`initramfs`如这里提到的[>>>](https://wiki.archlinux.org/index.php/NVIDIA#Pacman_hook)和[这里的> > >](https://wiki.archlinux.org/index.php/Kernel_mode_setting#Early_KMS_start) 。

编辑`/etc/mkinitcpio.conf`，在那里添加模块:

```
...
MODULES=(nouveau, nv)
... 
```

构建一个新的内核，使用*自定义名称*来避免覆盖现有的内核:

```
[root@setevoy-arch-pc setevoy]# mkinitcpio -g /boot/linux-nv.img
==> Starting build: 4.20.11-arch2-1-ARCH
-> Running build hook: [base]
-> Running build hook: [udev]
-> Running build hook: [autodetect]
-> Running build hook: [keyboard]
-> Running build hook: [keymap]
-> Running build hook: [modconf]
-> Running build hook: [block]
-> Running build hook: [lvm2]
-> Running build hook: [filesystems]
-> Running build hook: [fsck]
==> ERROR: module not found: `nouveau,'
==> ERROR: module not found: `nv'
==> Generating module dependencies
==> Creating gzip-compressed initcpio image: /boot/linux-nv.img
==> WARNING: errors were encountered during the build. The image may not be complete. 
```

嗯……这个解决方案显然有问题，正如我在晚上做的那样——我不想浪费时间寻找原因和/或正确的模块名称。

### NVIDIA 驱动程序安装–尝试 2(正确)

检查现在安装的 NVIDIA 相关包，注意描述:

```
[root@setevoy-arch-pc setevoy]# pacman -Qs nvidia
local/libvdpau 1.1.1+3+ga21bf7a-1
Nvidia VDPAU library
local/nvidia-lts 1:418.43-1
NVIDIA drivers for linux-lts
local/nvidia-utils 418.43-2
NVIDIA drivers utilities 
```

> 用于 linux-lts 的 NVIDIA 驱动程序

而这里我弄错了:内核的包 [`linux-lts`](https://www.archlinux.org/packages/core/x86_64/linux-lts/) ！= [`linux`](https://www.archlinux.org/packages/core/x86_64/linux/) 和分别`nvidia-lts`！= `nvidia`这不是一个有*长期支持*的驱动版本——而只是另一个内核版本的驱动。

移除此驱动程序:

```
[setevoy@setevoy-arch-pc ~]$ sudo pacman -Rsn nvidia-lts 
```

安装常用的`nvidia`包:

```
[setevoy@setevoy-arch-pc ~]$ sudo pacman -S nvidia 
```

再次重启电脑并检查现在使用的驱动程序:

```
[setevoy@setevoy-arch-pc ~]$ lspci -k | grep -A 2 -E "(VGA|3D)"
01:00.0 VGA compatible controller: NVIDIA Corporation GP107 [GeForce GTX 1050 Ti] (rev a1)
Subsystem: ASUSTeK Computer Inc. GP107 [GeForce GTX 1050 Ti]
Kernel driver in use: nvidia 
```

> 正在使用的内核驱动程序:nvidia

好吧。

### 显示器配置

现在要做的最后一件事是更新我的显示器设置。

有了`nouveau`他们就有了名字:

```
[setevoy@setevoy-arch-pc ~]$ cat .config/openbox/autostart
xrandr --output HDMI-1 --primary
xrandr --output HDMI-1 --left-of DVI-D-1 
```

但是现在:

```
[setevoy@setevoy-arch-pc .config]$ xrandr --listmonitors
Monitors: 2
0: +DVI-D-0 1920/530x1080/300+0+0  DVI-D-0
1: +HDMI-0 1920/531x1080/299+1920+0  HDMI-0 
```

更新 Openbox 的配置并用新的名字来设置它们:

```
[setevoy@setevoy-arch-pc ~]$ cat .config/openbox/autostart
# nouveau
xrandr --output HDMI-1 --primary
xrandr --output HDMI-1 --left-of DVI-D-1

# nvidia
xrandr --output HDMI-0 --primary
xrandr --output HDMI-0 --left-of DVI-D-0

# tools

setxkbmap -layout us,ru -option grp:lctrl\_lshift\_toggle,grp\_led:scroll -variant winkeys &
volumeicon &
qxkb & 
```

事实上，用`udev`规则来做更好——但是仍然找不到实现它的方法。

完成了。

### 类似的帖子

*   <small>03/10/2017</small>[Ubuntu:/usr/bin/X 未找到](https://rtfm.co.ua/ubuntu-usrbinx-not-found/)
*   T003/29/2017 t1t2arch:SKYPE-T3 音频和麦克风
*   T003/09/2017 t1t 2 arch:安装在 Ubuntu 上方，同时在 LVM T3 中保留 home
*   T003/12/2017 t1linux:LVM-拆分/root 和添加/home