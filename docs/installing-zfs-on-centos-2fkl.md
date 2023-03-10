# 在 CentOS 上安装 ZFS

> 原文：<https://dev.to/rgeraldporter/installing-zfs-on-centos-2fkl>

***备注**:我在 2013 年左右写了这篇文档，作为 [Weever Apps](https://weeverapps.com/) 的内部文档，用于设置 RAID-Z 作为快速备份 MySQL 的方法。由于我们不再使用这个设置，文档也不会再被使用，我觉得把它发布出来给那些可能会发现它有一些用处的人是个好主意。对 MySQL 使用 RAID-Z 是令人惊奇的，它从未让我们失望过，并且允许每小时进行基于快照的备份，没有停机时间。不过，我们后来转向了 AWS RDS 实例。*

这将是多篇文章的一部分，这些文章将允许你为 MySQL 建立一个 RAID-Z 设置。或者说，至少以前可以！虽然这是面向 Azure 的，但我确信它在任何地方都适用。

# 文章系列

1.  **在 CentOS 上安装 ZFS**
2.  [使用 CentOS 上的 ZFS 为 MySQL 创建 RAID-Z 驱动器](https://dev.to/rgeraldporter/creating-a-raid-z-drive-with-zfs-on-centos-3484)
3.  [设置用于 MySQL 主-主复制器模式的 RAID-Z](https://dev.to/rgeraldporter/setting-up-raid-z-for-use-in-a-mysql-master-master-replicator-pattern-4eme)
4.  [创建 MySQL 数据目录的 ZFS 映像](https://dev.to/rgeraldporter/creating-a-zfs-image-of-a-mysql-data-directory-dad)

随着我在接下来的几天里添加更多的部分，我会在这里添加链接。

# 创建虚拟机

首先，使用 CentOS 映像在 Azure 中创建一个虚拟机。使用 RAID-Z 至少需要 2 个内核。

确保它位于您希望连接的应用服务器虚拟机的云服务中。

初始用户的用户名应该是`azureadmin`，但是您应该确保更改密码并将其保存在安全的地方。

如果您希望利用负载平衡，请确保在被询问时创建一个可用性集，或者如果我们添加另一个负载平衡实例，请选择一个将与它一起使用的可用性集。

# 通过 SSH 登录后...

首先我们要成为须藤。

```
sudo su 
```

接下来，我们需要安装 ZFS，这需要一些设置。首先，我们需要禁用 SELinux，至少在 ZFS 软件升级到支持它之前(反之亦然)。第一个命令暂时禁用它，第二个命令永久禁用它。

```
echo 0 >/selinux/enforce
vim /etc/selinux/config 
```

并将`SELINUX=`改为`permissive`。

接下来，我们需要 yum 中的一些新内核库，所以我们需要编辑配置文件来删除内核异常。

```
vim /etc/yum.conf 
```

找到带有`exclude=kernel*`的那一行，用散列(`#`)注释掉它。

然后我们要安装一些东西。

```
yum groupinstall "Development Tools"
yum install kernel-devel zlib-devel libuuid-devel libblkid-devel libselinux-devel parted lsscsi nano mdadm bc 
```

接下来转到[zfsonlinux.org](//zfsonlinux.org)并点击 CentOS 链接，它应该有这样两个命令:

```
sudo yum localinstall --nogpgcheck http://archive.zfsonlinux.org/epel/zfs-release-1-3.el6.noarch.rpm
sudo yum install zfs 
```

这需要几分钟时间。

现在我们将加载模块。

```
modprobe zfs 
```

为了确保它的存在和工作，我们将使用这个命令进行检查。

```
lsmod | grep -i zfs 
```

让我们把它设置好，这样它会在启动时加载。

```
vim /etc/sysconfig/modules/zfs.modules 
```

粘贴到此文件:

```
#!/bin/sh

if [ ! -c /dev/zfs ] ; then exec /sbin/modprobe zfs >/dev/null 2>&1
fi 
```

保存文件(:wq)，然后我们将这样做:

```
chmod +x /etc/sysconfig/modules/zfs.modules 
```

现在让我们测试它的工作，并重新启动。

```
reboot 
```

然后确保我们从:
获得输出

```
lsmod | grep -i zfs 
```

如果是这样，ZFS 就安装好了！

某样东西严重过时了吗？这对你有用吗？请发表评论，以帮助其他可能使用此指导的人。