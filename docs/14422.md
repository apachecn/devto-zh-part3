# Minimal Arch Linux 安装指南:半小时内做好准备

> 原文：<https://dev.to/siatwe/install-a-minimal-arch-linux-in-half-an-hour--1l6p>

*   *编辑* 20191226:手动安装 linux 内核、nano 和 dhcpcd
*   *编辑* 20190809:最小设置不需要安装`base-devel`
*   *Edit* 20190318: Typo: sudoer = > sudoers
*   *编辑* 20190208:添加:如果`pacstrap -i`由于无效数据包(无效或损坏的数据包(PGP 签名))而无法正常运行的解决方案
*   *编辑* 20190205:新增:`loadkeys`安装期间 ___

*[原载于我的博客](https://siatwe.github.io/blog/index.html)*

一步一步的指导安装一个最小的 Arch Linux 操作系统与图形用户界面，终端模拟器和浏览器在短短 20 - 30 分钟。但是当然:

*   “最低”意味着一个分区，没有交换，没有特殊的驱动程序要求。

如果你想试试的话，我会推荐你先装在虚拟机上，这样就什么都破不了了。

* * *

默认的控制台键映射是 US。可用的布局可以用
列出

```
ls /usr/share/kbd/keymaps/**/*.map.gz 
```

然后加载您喜欢的布局:

```
loadkeys <lang-code> 
```

例如:

```
loadkeys de-latin1 
```

* * *

你连接到互联网了吗？你绝对需要互联网来安装！如果你的电脑连接到局域网电缆，这应该不是一个问题，除非你使用完全陌生的硬件。

*   n 次尝试

```
ping -c 3 google.com 
```

* * *

如果您没有连接到互联网，请检查您的可用网络接口...

*   `link`:管理和显示所有网络接口的状态

```
ip link 
```

输出可能是这样的:

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s25: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether xxxxxxxxxxxxxxxxx brd xxxxxxxxxxxxxxxxx
3: wlp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT group default qlen 1000
    link/ether xxxxxxxxxxxxxxxxx brd xxxxxxxxxxxxxxxxx 
```

...并尝试用 DHCP 配置自己喜欢的:

```
dhcpd <INTERFACE> 
```

例如:

```
dhcpd enp0s25 
```

* * *

或者使用您的无线局域网接口连接到互联网:

```
wifi-menu 
```

* * *

**更新包存储库:**

*   `-Syy`:同步软件包数据库而不升级它们

```
pacman -Syy 
```

* * *

**安装反射器包，该包根据给定的国家名称生成优化的镜像列表。(在德国的情况下):**

*   `-S`:安装特定或单个软件包

```
pacman -S reflector 
```

*   `-c`:引号中的国家
*   `-f`:最快的 n
*   `-l`:将列表限制为 n 个最近同步的服务器
*   `-n`:最多返回 n 面镜子
*   `--save`:保存到...

```
reflector -c "Germany" -f 12 -l 10 -n 12 --save /etc/pacman.d/mirrorlist 
```

* * *

**看看可用的硬盘及其分区。分区现在并不重要，但是它可以使识别你的磁盘变得更容易，并降低覆盖错误磁盘的风险:**

*   `-l`:列出指定设备的分区表

```
fdisk -l 
```

* * *

**在您选择的硬盘上运行 cfdisk，并创建一个主分区和可引导分区。重要的是不要将 cfdisk 应用于分区，而是应用于磁盘本身。/dev/sda！= /dev/sda1**

*   `X`:磁盘字母

```
cfdisk /dev/sdX 
```

* * *

创建分区后，必须对其进行格式化。例如使用 ext4 或 btrfs。现在重要的是选择分区:

*   `XY`:磁盘号+ `Y`:分区号

```
mkfs.ext4 /dev/sdXY 
```

* * *

**您现在必须将这样创建的分区挂载到/mnt:**

```
mount /dev/sdXY /mnt 
```

* * *

**下面的命令在给定的分区上安装基本系统:**

*   `-i`:需要时提示包确认(交互运行)

```
pacstrap -i /mnt base 
```

* * *

**如果数据包数据不正确(无效或损坏的数据包(PGP 签名))，运行以下命令并重试:**

```
pacman-key --init
pacman-key --populate archlinux
pacman-key --refresh-keys 
```

* * *

**安装完成后，您可以使用以下命令创建 fstab 文件:**

*   `-U`:使用 UUID(通用唯一标识符)作为源标识符(UUID 的快捷方式)
*   `-p`:避免打印伪 fs 挂载

```
genfstab -U -p /mnt >> /mnt/etc/fstab 
```

* * *

**现在 [chroot](https://wiki.archlinux.org/index.php/chroot) 进入你当前安装的系统用 bash:**

```
arch-chroot /mnt /bin/bash 
```

* * *

**安装纳米:**

```
pacman -S nano 
```

* * *

**编辑/etc/locale.gen，取消对 utf-8 和 en_US 的首选语言的注释。UTF-8 UTF-8:**

```
nano /etc/locale.gen 
```

例如:

```
de_DE.UTF-8 UTF-8 
en_US.UTF-8 UTF-8 
```

* * *

**现在，创建你的语言环境文件:**

```
locale-gen 
```

* * *

(如果您以后想通过互联网同步时间设置，可以跳过下面两点。)

**选择您的时区。(通过几个字符和 tab 键，您可以自动完成可能的大陆和城市):**

*   `-sf`:用符号链接代替硬链接，删除已有的目标文件

```
ln -sf /usr/share/zoneinfo/<CONTINENT>/<CITY> /etc/localtime 
```

例如:

```
ln -sf /usr/share/zoneinfo/Europe/Berlin /etc/localtime 
```

* * *

**更新你的硬件时钟:**

*   `--systohc`:系统 2 硬件时钟
*   `--utc`:世界协调时

```
hwclock --systohc --utc 
```

* * *

**给你的电脑起个名字(主机名):**

```
echo <HOSTNAME> > /etc/hostname 
```

* * *

**创建/编辑/etc/hosts 并添加你的主机名和一些需要的信息:**

```
nano /etc/hosts 
```

```
127.0.0.1   localhost.localdomain   localhost                    
::1              localhost.localdomain   localhost
127.0.1.1   localhost.localdomain   <HOSTNAME> 
```

* * *

**更改 root 密码:**

```
passwd 
```

* * *

安装 sudo、grub 和内核。可选:dialog、netctl、dhcpcd 和 wpa_supplicant(如果您已经用 wifi-menu 设置了互联网连接，那么您可以在下次重启后再次使用 wifi-menu:

```
pacman -S grub sudo dialog netctl wpa_supplicant dhcpcd linux linux-headers linux-firmware 
```

* * *

在你的硬盘上安装 Grub(不是分区)。如果您的计算机上有其他非 Linux/Unix 类的操作系统(例如 Windows ),并且希望能够通过 Grub 引导它们，然后安装 os-prober 并挂载它，Grub 应该能够识别该操作系统:

```
grub-install /dev/sdX 
```

* * *

**创建 grub 配置文件:**

*   `-o` : Out。指定输出文件

```
grub-mkconfig -o /boot/grub/grub.cfg 
```

* * *

此时，您应该在您的首选网络接口上启用 dhcpcd 守护程序，以避免每次新启动时都这样做。(如果您使用了 wifi-menu 并想继续使用它，则没有必要):

*   `enable`:启用此服务/守护程序...
*   `--now`:现在！您在这里并不真正需要这个参数，但是知道它的存在还是很好的

```
systemctl enable dhcpcd@<INTERFACE> --now 
```

* * *

**退出 chroot 环境:**

```
exit 
```

* * *

**卸载 arch 分区:**

*   `-R`:递归

```
umount -R /mnt 
```

* * *

**重启你的系统*(重要:你是否正确配置了 grub 并且没有错误信息？如果不是这样，您只能在某些情况下引导到新安装的系统中{再次从 live medium 引导，挂载现有的/mnt 分区，然后再次尝试安装 grub。所以你现在应该确定了):***

```
reboot 
```

* * *

**以 root 用户身份登录并创建一个新用户:**

*   `-m`:创建主目录
*   `-g`:新用户初始组的组名或 ID，在本例中为:users
*   `-G`:用户也是其成员的补充组的列表，在本例中:wheel(管理组，以后我们的 sudo 命令需要它)
*   `-s`:用户标准/登录 shell 的路径，此处:/bin/bash

```
useradd -m -g users -G wheel -s /bin/bash <USERNAME> 
```

* * *

**更改新用户的密码:**

```
passwd <USERNAME> 
```

* * *

**编辑/etc/sudoers 文件并取消对以下行的注释:**

```
nano /etc/sudoers 
```

```
%wheel     ALL=(ALL) ALL 
```

* * *

**退出根环境:**

```
exit 
```

* * *

**作为新创建的用户登录并安装 X，这是一个窗口管理器，有一个简单的菜单和状态栏，音频，一个终端模拟器和一个网络浏览器(在这个例子中 i3 有 dmenu 和 i3status，xfce4 终端和 chromium):**

```
sudo pacman -S pulseaudio pulseaudio-alsa alsa-utils xorg xorg-xinit i3-wm dmenu i3status chromium xfce4-terminal 
```

* * *

**把 i3 放在你的~/里。xinitrc 文件，所以`startx`知道怎么做:**

```
$ echo "exec i3" > ~/.xinitrc 
```

* * *

**开始 X:**

```
startx 
```

* * *

**没有声音？**

键入`win + enter`(打开已安装的终端)执行以下命令并重试:

```
alsactl init 
```

* * *

给你。现在您的计算机上有了一个最小的 Arch Linux 操作系统。没有垃圾，只有你想要的软件。一个小菜单在屏幕上方打开——从那里你可以启动你的程序。

* * *

**待办事项**

*   UEFI 安装