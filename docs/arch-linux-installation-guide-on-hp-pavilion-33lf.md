# HP Pavilion 上的 Arch Linux 安装指南

> 原文：<https://dev.to/baddate/arch-linux-installation-guide-on-hp-pavilion-33lf>

我的电脑详细信息:

> 电脑:惠普 Pavilion 笔记本电脑
> CPU:英特尔 i5-8250U
> GPU:英伟达 MX150

## 预安装

### 验证开机模式

要验证是否是 UEFI 模式，使用此命令:
`ls /sys/firmware/efi/efivars`
如果它显示一些细节，则系统是 UEFI。否则，系统可能会在 BIOS 或 CSM 模式下启动。

### 连接互联网

因为我用的是惠普笔记本电脑。所以我只需要用这个命令`wifi-menu`连接到 wifi。如果您想连接到有线网络，请参考官方指南关于 [dhcpcd](https://wiki.archlinux.org/index.php/Dhcpcd#Installation)
您可以通过`ping www.archlinux.org`检查此连接。

### 更新系统时钟

使用`timedatectl set-ntp true`确保系统时钟准确，使用`timedatectl status`检查服务状态。

### 将磁盘分区

使用`lsblk`或`fdisk -l`来验证设备。
首先运行`fdisk /dev/sda`(注意:如果你的设备是/dev/sdX，运行`fdisk /dev/sdX`，你会进入 *fdisk* 对话框。
下一步输入`m`你将获得帮助详情。如果你使用独立的硬盘。输入`g`将其格式化为 GPT。
接下来，输入`n`创建新分区，如果完成，输入`w`退出。

这是我的分区布局:

| 挂载点 | 划分 | 分区类型 | 大小 |
| --- | --- | --- | --- |
| /mnt | /dev/sda3 | linux 文件系统 | 50 克 |
| /mnt/boot | /dev/sda2 | linux 文件系统 | 500 米 |
| /mnt/boot/EFI | /dev/sda1 | EFI 系统分区 | 500 米 |
| [互换] | /dev/sda4 | linux 交换 | 16G |
| /mnt/home | /dev/sda5 | linux 文件系统 | 300 克 |

### 格式化分区

如果 EFI 分区在/dev/sdX0 上，运行:
`mkfs.fat -F32 /dev/sdX0`

如果根分区在* /dev/sdX1*上，并且将包含 ext4 文件系统，运行:
`mkfs.ext4 /dev/sdX1`

如果您为 swap 创建了一个分区，用 mkswap:
初始化它

```
mkswap /dev/sdX2
swapon /dev/sdaX2 
```

如果您使用我的布局，请运行以下命令:

```
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
mkfs.ext4 /dev/sda3
mkfs.ext4 /dev/sda5
mkswap /dev/sda4
swapon /dev/sda4 
```

### 挂载文件系统

如果您使用我的布局，请运行以下命令:

```
mount /dev/sda3 /mnt
mkdir /mnt/boot
mount /dev/sda2 /mnt/boot
mkdir /mnt/boot/EFI
mount /dev/sda1 /mnt/EFI
mkdir /mnt/home
mount /dev/sda5 /mnt/home 
```

## 安装

### 选择镜像

*   通过`nano /etc/pacman.d/mirrorlist`打开镜像列表文件
*   取消对所选服务器的注释。
*   保存更改###安装基础包使用`pacstrap /mnt base base-devel` ##配置系统###生成 fstab 文件
*   使用`genfstab -U /mnt >> /mnt/etc/fstab`
*   检查是否有错误:`cat /mnt/etc/fstab` ###进入新系统运行`arch-chroot /mnt` ###时区和位置
*   设置时区:
    `ln -sf /usr/share/zoneinfo/Region/City /etc/localtime`

*   设置硬件时钟:
    `hwclock --systohc`

*   设置位置

*   取消注释 en_US。在 **/etc/locale.gen** 中 UTF-8 UTF-8 和其他需要的区域设置，并生成:

```
nano /etc/locale.gen 
locale-gen 
```

对了，新系统没有`vim`。

1.  创建`locale.conf`并设置`LANG`变量:

```
nano /etc/locale.conf
LANG= en_US.UTF-8 UTF-8 
```

### 网络配置

*   创建*主机名*文件，并输入您的主机名:`nano /etc/hostname`。
*   将匹配条目添加到*主机*:

```
127.0.0.1   localhost
::1     localhost
127.0.1.1   myhostname.localdomain  myhostname 
```

### 设置 root 密码

运行`passwd`

### 启用微码更新

```
pacman -S intel-ucode # for intel CPU
pacman -S amd-ucode # for amd CPU 
```

### 安装并设置引导程序

我使用 [grub](https://en.wikipedia.org/wiki/GNU_GRUB) ，所以:

*   安装相关包:`pacman -S grub dosfstools efibootmgr`
*   安装 grub `grub-install --target=x86_64-efi --efi-directory=/boot/EFI --recheck`
*   使用 grub-mkconfig 工具生成/boot/grub/grub.cfg: `grub-mkconfig -o /boot/grub/grub.cfg`

### 安装无线局域网工具

我用`wifi-menu`连接 wifi，所以需要这个包:
`pacman -S wpa_supplicant dialog iw`

### 添加一个用户

*   添加用户`useradd -m -g users -s /bin/bash username`
*   为用户`passwd username`设置密码
*   权限提升(sudo) `nano /etc/sudoers`并在`root ALL=(ALL) ALL`下面添加`username ALL=(ALL) ALL`

## 安装后

### 安装显示服务器

运行最小命令:`pacman -S xorg-server`。

### 安装显示器驱动程序

因为我用的是*NVIDIA MX150*显卡，所以用这个`xf86-video-nouveau`驱动。详细情况你可以参考[这个](https://wiki.archlinux.org/index.php/NVIDIA)。如果用 *amd 卡*，可以读到[这个](https://wiki.archlinux.org/index.php/Xorg#AMD)

* * *

**感谢您的阅读！**
如果有发现我会继续更新这篇文章。