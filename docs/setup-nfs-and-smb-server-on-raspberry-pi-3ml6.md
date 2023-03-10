# 在 Raspberry Pi 上设置 NFS 和 SMB 服务器

> 原文：<https://dev.to/pratham/setup-nfs-and-smb-server-on-raspberry-pi-3ml6>

### NFS 服务器

*   安装服务器

```
 sudo apt install nfs-common nfs-server 
```

Enter fullscreen mode Exit fullscreen mode

*   创建您想要共享的目录

```
 mkdir /media/storage 
```

Enter fullscreen mode Exit fullscreen mode

*   用以下内容编辑文件`/etc/exports`

```
 /media/storage 192.168.1.0/24(rw,all_squash,insecure,no_subtree_check)
  # <directory> <who_can_access> <options> 
```

Enter fullscreen mode Exit fullscreen mode

*   重启服务

```
 sudo service nfs restart
  /etc/init.d/nfs-kernel-server restart 
```

Enter fullscreen mode Exit fullscreen mode

*   从客户端连接(MacOS)

```
 mkdir /Users/username/nfs_shared
  sudo mount -v -o "resvport" 192.168.1.9:/media/storage /Users/username/nfs_shared 
```

Enter fullscreen mode Exit fullscreen mode

### 桑巴服务器

*   安装服务器

```
 sudo apt-get install samba samba-common-bin 
```

Enter fullscreen mode Exit fullscreen mode

*   为系统用户创建 samba 密码

```
 sudo smbpasswd -a pi 
```

Enter fullscreen mode Exit fullscreen mode

*   创建您想要共享的目录

```
 mkdir /media/storage 
```

Enter fullscreen mode Exit fullscreen mode

*   用以下内容编辑文件`/etc/samba/smb.conf`

```
 [PI]
  comment = Pi workspace
  path = /media/storage
  create mask = 0775
  directory mask = 0775
  read only = no
  browseable = yes public = no
  force user = pi
  only guest = no 
```

Enter fullscreen mode Exit fullscreen mode

*   重新启动服务

```
 sudo service samba restart 
```

Enter fullscreen mode Exit fullscreen mode

*   从客户端连接(MacOS)

```
 mkdir /Users/username/smb_shared
  mount -t smbfs //pi@192.168.1.9/pi /Users/username/smb_shared 
```

Enter fullscreen mode Exit fullscreen mode