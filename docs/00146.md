# 使用 CentOS 上的 ZFS 为 MySQL 创建 RAID-Z 驱动器

> 原文：<https://dev.to/rgeraldporter/creating-a-raid-z-drive-with-zfs-on-centos-3484>

***备注**:我在 2013 年左右写了这篇文档，作为 [Weever Apps](https://weeverapps.com/) 的内部文档，用于设置 RAID-Z 作为快速备份 MySQL 的方法。由于我们不再使用这个设置，文档也不会再被使用，我觉得把它发布出来给那些可能会发现它有一些用处的人是个好主意。对 MySQL 使用 RAID-Z 是令人惊奇的，它从未让我们失望过，并且允许每小时进行基于快照的备份，没有停机时间。不过，我们后来转向了 AWS RDS 实例。*

这将是多篇文章的一部分，这些文章将允许你为 MySQL 建立一个 RAID-Z 设置。或者说，至少以前可以！虽然这是面向 Azure 的，但我确信它在任何地方都适用。

随着我在接下来的几天里添加更多的部分，我会在这里添加链接。

# 文章系列

1.  [在 CentOS 上安装 ZFS](https://dev.to/rgeraldporter/installing-zfs-on-centos-2fkl)
2.  **使用 CentOS 上的 ZFS 为 MySQL 创建 RAID-Z 驱动器**
3.  [设置用于 MySQL 主-主复制器模式的 RAID-Z](https://dev.to/rgeraldporter/setting-up-raid-z-for-use-in-a-mysql-master-master-replicator-pattern-4eme)
4.  [创建 MySQL 数据目录的 ZFS 映像](https://dev.to/rgeraldporter/creating-a-zfs-image-of-a-mysql-data-directory-dad)

# 指令

首先，让我们确保我们已经安装了 ZFS。

```
sudo su
lsmod | grep -i zfs 
```

接下来，在 Azure 中，关闭服务器，然后连接所有想要用于 RAID-Z 的磁盘；最低应为使用最多 4 个磁盘的双核计算机。所有磁盘的大小应该相同，尽管这可能不是 RAID-Z 的要求。

对于本例，我将制作 4 个磁盘，每个 300 GB。通过确保 raid-z 在名称中的某个位置，为它们命名，以便清楚地表明它们是 RAID-Z 磁盘。

每个磁盘大约需要一分钟来安装。

添加最后一个磁盘后，启动机器。

重新启动后，检查磁盘的状态(确保`sudo su`):

```
fdisk -l | grep GB 
```

以下命令将创建一个名为`sqlstorage`的 zpool，并将其挂载到`/etc/mysql`:

```
zpool create -m /var/lib/mysql sqlstorage raidz -f sdc sdd sde sdf 
```

请注意，`raidz`的最小值是两个磁盘。对于被称为`raidz2`的双奇偶校验`raidz`，你至少需要 3 个磁盘。对于三重，需要 4 个磁盘。在设置中尽可能使用最高级别的 RAID-Z。

要检查 zpool 状态:

```
zpool status 
```

要确保它已装载:

```
mount | grep zfs 
```

并确认存储大小:

```
df -h | grep sqlstorage 
```

我们还希望确保驱动器将在启动时装载:

```
echo "zfs mount sqlstorage" >> /etc/rc.local 
```

重新启动，然后您可以重复上述检查，以确保它在启动时工作。

某样东西严重过时了吗？这对你有用吗？请发表评论，以帮助其他可能使用此指导的人。