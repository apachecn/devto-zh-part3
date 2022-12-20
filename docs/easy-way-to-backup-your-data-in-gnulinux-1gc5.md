# 在 GNU/Linux 中备份数据的简单方法

> 原文：<https://dev.to/aris/easy-way-to-backup-your-data-in-gnulinux-1gc5>

数据对我们来说非常重要。数据丢失对每个人来说都是一场灾难，包括系统管理员。因此，有必要定期备份您的数据。在 Linux 中我们可以很容易地自动和定时备份数据。下面将解释如何操作。

## 步骤 01–设置 ssh

应该完成这一步，因为我们将把备份文件上传到远程服务器。在这个阶段，我们将安排无密码登录 SSH。

创建 RSA 密钥:

```
mkdir -p ~/.ssh  
touch ~/.ssh/authorized_keys  
ssh-keygen -t rsa -b 2048  
chmod go-w ~/  
chmod 700 ~/.ssh  
chmod 600 ~/.ssh/* 
```

Enter fullscreen mode Exit fullscreen mode

上传公钥到备份服务器:

```
ssh-copy-id -i ~/.ssh/id_rsa.pub '-o StrictHostKeyChecking=no REMOTEUSER@REMOETHOST -pREMOTEPORT' 
```

Enter fullscreen mode Exit fullscreen mode

在本地和远程服务器上创建备份目录:

```
mkdir -p /var/backup; chown -R REMOTEUSER /var/backup  
ssh REMOTEUSER@REMOETHOST -pREMOTEPORT 'sudo mkdir -p /var/backup; sudo chown -R REMOTEUSER /var/backup' 
```

Enter fullscreen mode Exit fullscreen mode

## 步骤 02–安装备份 2l

```
# In RedHat based distros:
yum update  
yum install backup2l

# In Debian based distros:
apt-get update  
apt-get install backup2l 
```

Enter fullscreen mode Exit fullscreen mode

或者您可以从源代码构建它。有关更多信息，请阅读官方文档。

## 步骤 03–配置备份 2l

编辑`/etc/backup2l.conf`并创建如下配置:

```
FOR_VERSION=1.5  
VOLNAME="all"  
SRCLIST=(/etc/apache2/sites-available /home)  
SKIPCOND=(-path "/home/REMOTEUSER" "/etc/apache2/sites-available/default*" "*.nobackup*" -o -name "*.o")  
BACKUP_DIR="/var/backup"  
MAX_LEVEL=3  
MAX_PER_LEVEL=9  
MAX_FULL=2  
GENERATIONS=1  
CREATE_CHECK_FILE=1  
AUTORUN=0  
SIZE_UNITS="M"  
UNCONFIGURED=0  
CREATE_DRIVER="DRIVER_TAR_GZ"  
PRE_BACKUP () {  
  echo -e "  Backup MySQL Databases:"
  echo -e "  -----------------------"
  /usr/bin/mysqldump -u root -p --all-databases --skip-lock-tables | bzip2 > /home/all_db.sql.bz2
}
POST_BACKUP () {  
  echo -e "  Uploading Backup File to Backup Server:"
  echo -e "  ---------------------------------------"
  /usr/bin/scp -r /var/backup/all.*.tar.gz REMOTEUSER@REMOTEHOST:/var/backup/.
} 
```

Enter fullscreen mode Exit fullscreen mode

为自动备份创建 Cron，在这个例子中，我只是每周创建自动备份。你可以随意编辑。

```
cat > /etc/cron.d/autobackup <<EOF @weekly /usr/sbin/backup2l > /dev/null || nice -n 19 ionice -c 3 backup2l -b EOF 
```

Enter fullscreen mode Exit fullscreen mode

**重要提示:**您可以根据需要修改配置。记得更改 **REMOTEUSER** 、 **REMOTEHOST** 和 **REMOTEPORT** 变量。