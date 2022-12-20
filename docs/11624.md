# Linux:woe USB–创建 Windows USB 启动闪存驱动器

> 原文：<https://dev.to/setevoy/linux-woeusb--creating-windows-usb-boot--flash-drive-125j>

[![](img/68333939c95a5939a0f84006f4da18c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8fVOoVHf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2011/08/linux-logo.png) 嗯——有时候不得不做一点古怪的事情……

今天需要从 Linux 创建可启动的 USB 闪存驱动器和 Windows 10。

有各种各样的解决方案，但今天我发现了 [`woeusb`](https://github.com/slacka/WoeUSB) 实用程序，它工作得很好，而且它可以将一个驱动器格式化为一个必要的文件系统。

安装在 AUR 的 Arch Linux 上:

```
$ yaourt -S woeusb 
```

找到你的闪存盘:

```
$ lsblk
NAME             MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda                8:0    0   477G  0 disk
├─sda1             8:1    0     1G  0 part /boot
├─sda2             8:2    0    16G  0 part [SWAP]
└─sda3             8:3    0   460G  0 part
├─vg_arch-root 254:0    0    50G  0 lvm  /
└─vg_arch-home 254:1    0   410G  0 lvm  /home
sdb                8:16   0 931.5G  0 disk
└─sdb1             8:17   0 931.5G  0 part /backups
sdc                8:32   1  14.3G  0 disk
└─sdc1             8:33   1  14.3G  0 part 
```

在这种情况下是这样的。

运行 WoeUSB:

```
$ sudo woeusb --device Downloads/Win10_1809Oct_English_x64.iso /dev/sdc
WoeUSB v@@WOEUSB_VERSION@@
==============================
Mounting source filesystem...

Error: File "/media/woeusb_source_1550995484_30256/sources/install.wim" in source image has exceed the FAT32 Filesystem 4GiB Single File Size Limitation and cannot be installed.  You must specify a different --target-filesystem.

Refer: https://github.com/slacka/WoeUSB/wiki/Limitations#fat32-filesystem-4gib-single-file-size-limitation for more info.

Unmounting and removing "/media/woeusb_source_1550995484_30256"...

You may now safely detach the target device 
```

好吧…

检查设备上的文件系统类型:

```
$ sudo fdisk -l /dev/sdc
Disk /dev/sdc: 14.3 GiB, 15376000000 bytes, 30031250 sectors
...
Device     Boot Start      End  Sectors  Size Id Type

/dev/sdc1  *     2048 30029823 30027776 14.3G  c W95 FAT32 (LBA) 
```

这里> > > 的文档中有现成的解决方案。

使用`--target-filesystem`选项再次运行`woeusb`，并设置 NTFS:

```
$ sudo woeusb --target-filesystem NTFS --device Downloads/Win10_1809Oct_English_x64.iso /dev/sdc

WoeUSB v@@WOEUSB_VERSION@@
==============================
Mounting source filesystem...
Wiping all existing partition table and filesystem signatures in /dev/sdc...
...
Creating NTFS volume structures.
mkntfs completed successfully. Have a nice day.
...
Mounting target filesystem...
Copying files from source media...
...
Installing GRUB bootloader for legacy PC booting support...
Installing for i386-pc platform.
Installation finished. No error reported.
Installing custom GRUB config for legacy PC booting...
Resetting workaround to prevent 64-bit systems with big primary memory from being unresponsive during copying files.
Unmounting and removing "/media/woeusb_source_1550995613_701"...
Unmounting and removing "/media/woeusb_target_1550995613_701"...
You may now safely detach the target device
Done!
The target device should be bootable now 
```

完成了。

### 类似的帖子

*   <small>02/24/2019</small> [Arch Linux:用 EFI 和 Windows 双引导安装](https://dev.to/setevoy/arch-linux-installing-with-efi-and-windows-dual-boot-1eoh) <small>(0)</small>
*   t04/09/2017 年 t1t2arch:安装 NTFS T3t 4
*   <small>03/18/2017</small>[【Linux:LVM】【home】，【root】](https://rtfm.co.ua/linux-lvm-umenshit-home-uvelichit-root/)<small></small>
<small>*   <small>02/24/2019</small>[Arch Linux:установкасEFIи双启动с Windows](https://rtfm.co.ua/arch-linux-ustanovka-s-efi-i-dual-boot-s-windows/) <small>(0)</small></small>

<small></small>