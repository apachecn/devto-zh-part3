# 利用 LVM 缓存

> 原文：<https://dev.to/jsrnop/leveraging-lvm-caching-1695>

注意:我正在把我写的相关博客文章转移到 dev.to。这是第一篇。

* * *

我最近在很大程度上基于 [Richard WM Jones 的帖子](https://rwmj.wordpress.com/2014/05/22/using-lvms-new-cache-feature/)实现了 [LVM 缓存](http://man7.org/linux/man-pages/man7/lvmcache.7.html)，并认为应该在这里记录我自己的经历。

我从一个包含`/`、`/home`和`swap`的 SSD，两个 2 TB 的 HDD(`sdb`和`sdc`)和一个额外的 SSD ( `sdd`)开始。

```
[root@bender ~]# lsblk
NAME            MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda               8:0    0 447.1G  0 disk
├─sda1            8:1    0   500M  0 part /boot
└─sda2            8:2    0 446.7G  0 part
  ├─fedora-root 253:0    0    50G  0 lvm  /
  ├─fedora-swap 253:1    0  15.7G  0 lvm  [SWAP]
  └─fedora-home 253:2    0   381G  0 lvm  /home
sdb               8:16   0   1.8T  0 disk
sdc               8:32   0   1.8T  0 disk
sdd               8:48   0 119.2G  0 disk 
```

Enter fullscreen mode Exit fullscreen mode

然后我使用`fdisk`在每个硬盘上创建一个`Linux`分区，在固态硬盘上创建一个`Linux LVM`分区。不过，除了美观之外，这些分区类型实际上对我们的目的并不重要。

```
[root@bender ~]# lsblk
NAME            MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda               8:0    0 447.1G  0 disk
├─sda1            8:1    0   500M  0 part /boot
└─sda2            8:2    0 446.7G  0 part
  ├─fedora-root 253:0    0    50G  0 lvm  /
  ├─fedora-swap 253:1    0  15.7G  0 lvm  [SWAP]
  └─fedora-home 253:2    0   381G  0 lvm  /home
sdb               8:16   0   1.8T  0 disk
└─sdb1            8:17   0   1.8T  0 part
sdc               8:32   0   1.8T  0 disk
└─sdc1            8:33   0   1.8T  0 part
sdd               8:48   0 119.2G  0 disk
└─sdd1            8:49   0 119.2G  0 part 
```

Enter fullscreen mode Exit fullscreen mode

然后，我使用两个硬盘分区创建了一个 RAID 1 集。

```
[root@bender ~]# mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
[root@bender ~]# echo 'DEVICE /dev/sdb1 /dev/sdc1' > /etc/mdadm.conf
[root@bender ~]# mdadm --detail --scan >> /etc/mdadm.conf
[root@bender ~]# cat /etc/mdadm.conf
DEVICE /dev/sdb1 /dev/sdc1
ARRAY /dev/md0 metadata=1.2 name=bender:0 UUID=2f9fd7a2:cf0d4dd8:579ec7ce:073d0886 
```

Enter fullscreen mode Exit fullscreen mode

然后，我创建了 LVM 卷，并在被抓取的卷上创建了一个`ext4`文件系统。

```
[root@bender ~]# pvcreate /dev/md0 /dev/sdd1
  Physical volume "/dev/md0" successfully created
  Physical volume "/dev/sdd1" successfully created
[root@bender ~]# vgcreate data /dev/md0
  Volume group "data" successfully created
[root@bender ~]# lvcreate -n vol0 -L 1.5T data /dev/md0
  Logical volume "vol0" created.
[root@bender ~]# lvs
  LV   VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  vol0 data   -wi-a-----   1.50t
  home fedora -wi-ao---- 380.95g
  root fedora -wi-ao----  50.00g
  swap fedora -wi-ao----  15.69g
[root@bender ~]# pvs
  PV         VG     Fmt  Attr PSize   PFree
  /dev/md0   data   lvm2 a--    1.82t 326.89g
  /dev/sda2  fedora lvm2 a--  446.64g   4.00m
  /dev/sdd1         lvm2 ---  119.24g 119.24g
[root@bender ~]# mkfs.ext4 /dev/data/vol0
mke2fs 1.42.13 (17-May-2015)
Creating filesystem with 402653184 4k blocks and 100663296 inodes
Filesystem UUID: 51e8796a-5090-45ce-ae62-4944f2eb9132
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
        102400000, 214990848

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done 
```

Enter fullscreen mode Exit fullscreen mode

现在来看看缓存配置。在下面的命令中指定实际的分区(`sdd1`)很重要，这样可以确保缓存是在该设备上创建的，而不是在卷组中任意创建的。

```
[root@bender ~]# vgextend data /dev/sdd1
  Volume group "data" successfully extended
[root@bender ~]# lvcreate -L 1G -n vol0_cache_meta data /dev/sdd1
  Logical volume "vol0_cache_meta" created.
[root@bender ~]# lvcreate -L 115G -n vol0_cache data /dev/sdd1
  Logical volume "vol0_cache" created.
[root@bender ~]# lvs
  LV              VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  vol0            data   -wi-a-----   1.50t
  vol0_cache      data   -wi-a----- 115.00g
  vol0_cache_meta data   -wi-a-----   1.00g
  home            fedora -wi-ao---- 380.95g
  root            fedora -wi-ao----  50.00g
  swap            fedora -wi-ao----  15.69g
[root@bender ~]# lvconvert --type cache-pool --poolmetadata data/vol0_cache_meta data/vol0_cache
  WARNING: Converting logical volume data/vol0_cache and data/vol0_cache_meta to pool's data and metadata volumes.
  THIS WILL DESTROY CONTENT OF LOGICAL VOLUME (filesystem etc.)
Do you really want to convert data/vol0_cache and data/vol0_cache_meta? [y/n]: y
  Converted data/vol0_cache to cache pool.
[root@bender ~]# lvconvert --type cache --cachepool data/vol0_cache data/vol0
  Logical volume data/vol0 is now cached.
[root@bender ~]# lvs
  LV   VG     Attr       LSize   Pool         Origin       Data%  Meta%  Move Log Cpy%Sync Convert
  vol0 data   Cwi-a-C---   1.50t [vol0_cache] [vol0_corig] 0.00   1.43            100.00
  home fedora -wi-ao---- 380.95g
  root fedora -wi-ao----  50.00g
  swap fedora -wi-ao----  15.69g 
```

Enter fullscreen mode Exit fullscreen mode

然后我在`/var/data`装上了。

```
[root@bender ~]# echo '/dev/mapper/data-vol0 /var/data                 ext4    defaults        1 2' >> /etc/fstab
[root@bender ~]# mount -a
[root@bender ~]# df -h /var/data
Filesystem             Size  Used Avail Use% Mounted on
/dev/mapper/data-vol0  1.5T   69M  1.5T   1% /var/data 
```

Enter fullscreen mode Exit fullscreen mode