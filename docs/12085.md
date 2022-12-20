# 使用时髦的分区布局进行加密

> 原文：<https://dev.to/jsrnop/encryption-with-a-funky-partition-layout-1pdf>

注:要了解我如何设置 LVM 缓存，请查看我的前一篇文章。

* * *

我最近在摆弄系统的时候遇到了一些麻烦，于是我决定抓住机会重组我的分区布局，重新安装一个完整的操作系统；这次用的是全磁盘加密(除了`/boot`)。可以说，Fedora 安装程序相当灵活，但还不足以支持 mdadm、LUKS 和 LVM 的混合。

在我粗略地告诉你我是如何做到的之前，这里是我的结果:

```
NAME                                              MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
sda                                                 8:0    0 447.1G  0 disk
├─sda1                                              8:1    0   512M  0 part  /boot
└─sda2                                              8:2    0 446.6G  0 part
  ├─vg_SIIIKE-root                                253:0    0  59.6G  0 lvm
  │ └─luks-SIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIKE   253:3    0  59.6G  0 crypt /
  ├─vg_SIIIKE-lv_swap                             253:1    0  14.9G  0 lvm
  │ └─luks-SIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIKE   253:2    0  14.9G  0 crypt [SWAP]
  └─vg_SIIIKE-home                                253:4    0 372.1G  0 lvm
    └─luks-SIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIKE   253:9    0 372.1G  0 crypt /home
sdb                                                 8:16   0 119.2G  0 disk
└─sdb1                                              8:17   0 119.2G  0 part
  ├─vg_SIIIKE-lv_var_cache_cdata                  253:5    0   115G  0 lvm
  │ └─vg_SIIIKE-lv_var                            253:8    0   1.8T  0 lvm
  │   └─luks-SIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIKE 253:10   0   1.8T  0 crypt /var
  └─vg_SIIIKE-lv_var_cache_cmeta                  253:6    0     1G  0 lvm
    └─vg_SIIIKE-lv_var                            253:8    0   1.8T  0 lvm
      └─luks-SIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIKE 253:10   0   1.8T  0 crypt /var
sdc                                                 8:32   0   1.8T  0 disk
└─md0                                               9:0    0   1.8T  0 raid1
  └─vg_SIIIKE-lv_var_corig                        253:7    0   1.8T  0 lvm
    └─vg_SIIIKE-lv_var                            253:8    0   1.8T  0 lvm
      └─luks-SIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIKE 253:10   0   1.8T  0 crypt /var
sdd                                                 8:48   0   1.8T  0 disk
└─md0                                               9:0    0   1.8T  0 raid1
  └─vg_SIIIKE-lv_var_corig                        253:7    0   1.8T  0 lvm
    └─vg_SIIIKE-lv_var                            253:8    0   1.8T  0 lvm
      └─luks-SIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIIKE 253:10   0   1.8T  0 crypt /var 
```

Enter fullscreen mode Exit fullscreen mode

为了澄清，我有一个上面有`/`、`/home`和`swap`的 SSD 和一个前面有 SSD 作为缓存层的软件 RAID-1 集。分区(`sda2`、`sdb1`和`md0`)都是同一个卷组中的物理卷。我只是在那个卷组中创建了逻辑卷，并指示它们驻留在某些物理卷上(这是一个非常方便的特性)。然后就是用 LUKS 加密它们，配置`crypttab`，配置`fstab`。

不幸的是，在 Fedora 桌面安装程序中你只能完成很少的任务。我最初试图在安装程序之外配置所有东西，希望它能得到提示，不去管它。问题是工作站版本不知道如何正确处理软件 RAID 集。我尝试使用服务器版本，但它通过对我想如何做 LVM 做某些假设，自行其是。

最后，由于实际上只有`/var`是特殊的，我决定将所有东西安装在主 SSD 上，然后在安装后配置`/var`卷，并将新安装的`/var`迁移到它的新家。这比预期的要好得多，除了不得不启动`single`来修复一个肥手指`fstab`之外，一切都启动了。

虽然我不认为 Fedora 安装程序需要生成任意分区方案的能力，但拥有某种“我知道我在做什么，请不要管我的分区”模式会很好。