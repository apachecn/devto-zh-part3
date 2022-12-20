# 安装和配置 FTP 服务器

> 原文：<https://dev.to/nuculabs_dev/installing-and-configuring-a-ftp-server-47fm>

在花了大部分周末时间摆弄 VSFTPD 之后，我很高兴我设法让它正常工作。

我的目标是为我的家庭网络创建一个简单、匿名和私有的 FTP 服务器。它应该有助于在我的机器之间共享文件。

## 配置服务器

如果你想试试我的配置，你只需要看看下面的命令，用我的替换你的配置文件。配置文件将在本文后面提供。

```
# For Ubuntu
sudo apt-get install vsftpd
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.orig

# Make the directories for the server
sudo mkdir -p /srv/vsftpd/root/public
sudo touch /srv/vsftpd/banner

# Change permissions and set ownership
sudo chown -R ftp:ftp /srv/vsftpd/ /srv/vsftpd/banner
sudo chmod -R 555 /srv/vsftpd/ /srv/vsftpd/banner
sudo chmod 775 /srv/vsftpd/root/public

# Edit the configuration file
# /etc/vsftpd.conf with editor of choice

# Restart the service and check the status
sudo systemctl restart vsftpd
sudo systemctl status vsftpd 
```

Enter fullscreen mode Exit fullscreen mode

我使用的配置文件 vsftpd.conf 可以在下面找到:

```
# Nucu Labs's BUSY vsFTPd server configuration file.
# Denis Nutiu 09.02.2019 
# Scope of the server: The server is supposed to facilitate easy file sharing and should
# be only available on the local network. No outside access is allowed! 
# The meaning of this configuration file and the full list of options can be found by
# checking the manual page of vsftpd. 
listen=NO
listen_ipv6=YES

### Debug Options 
# If enabled, a log file will be maintained detailing uploads and downloads. 
# By default, this file will be placed at /var/log/vsftpd.log, xferlog_enable=YES

# debug_ssl=YES
# log_ftp_protocol=YES
# syslog_enable=YES 
### 
# If enabled, both the usernames ftp and anonymous are recognised as anonymous logins. anonymous_enable=YES

# This option represents a directory which vsftpd will try to change into after an anonymous login. 
# Failure is silently ignored. anon_root=/srv/vsftp/root
local_root=/srv/vsftp/root
allow_writeable_chroot=YES

# Enable local users to login to the FTP. local_enable=NO

# This option is the name of a file containing text to display when someone connects to the server. banner_file=/srv/vsftp/banner

# When enabled, this prevents vsftpd from asking for an anonymous password 
# - the anonymous user will log straight in. no_anon_password=YES

# If set to YES, anonymous users will be permitted to create new directories under certain conditions.
# For this to work, the option write_enable must be activated, 
# and the anonymous ftp user must have write permission on the parent directory. anon_mkdir_write_enable=YES

# If set to YES, anonymous users will be permitted to perform write operations 
# other than upload and create directory, such as deletion and renaming. 
# This is generally not recommended but included for completeness. anon_other_write_enable=YES

# If set to YES, anonymous users will be permitted to upload files under certain conditions. anon_upload_enable=YES

# When enabled, anonymous users will only be allowed to download files which are world readable. anon_world_readable_only=YES

# If enabled, all anonymously uploaded files will have the ownership changed 
# to the user specified in the setting chown_username chown_uploads=YES
chown_username=ftp
nopriv_user=ftp

delete_failed_uploads=YES

# If enabled, users of the FTP server can be shown messages when they first enter 
# a new directory. By default, a directory is scanned for the file .message. dirmessage_enable=YES

# If enabled, all user and group information in directory listings will be displayed as "ftp". hide_ids=YES

# Allows: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE and SITE. write_enable=YES

# Upload files are executable. chown_upload_mode=0777
file_open_mode=0777
local_umask=002
anon_umask=002

### 
# Use the system's local time in GMT. use_localtime=YES

# This option should be the name of a directory which is empty.  Also, the
# directory should not be writable by the ftp user. This directory is used
# as a secure chroot() jail at times vsftpd does not require filesystem
# access. secure_chroot_dir=/var/run/vsftpd/empty

# This string is the name of the PAM service vsftpd will use. pam_service_name=vsftpd

# Uncomment this to indicate that vsftpd use a utf8 filesystem. utf8_filesystem=YES 
```

Enter fullscreen mode Exit fullscreen mode

使用这种设置将为您提供一个完全工作的匿名 VSFTPD 服务器。但是等等！确保不要将服务器暴露在互联网上！只供私人使用，因为服务器是匿名的，每个人都可以下载和上传文件。

## 排除服务器故障

我很难获得正确的权限，如果你没有获得正确的权限，你会发现自己无法上传文件，下载文件，创建目录或 cd 目录。

我遇到的最常见的错误是**可写根。chroot()的所有者是 root，并且具有写权限。**已通过从 */srv/vsftpd/root* 目录中移除写权限修复了此问题。

另一件你可能想要的事情是把你的用户添加到 ftp 组。当我添加自己时，我确实错过了-a，最后我从所有组中删除了自己，除了 ftp 组。我不得不在恢复模式下启动 Ubuntu，获得一个根 shell，并把自己添加回 sudo 和其他管理组。要将自己添加到 ftp 组，您可以安全地复制粘贴以下命令:`sudo usermod -a -G ftp $USER`。

如果横幅文件(上面用 touch 命令创建的文件)不存在或权限不足，服务器将不会启动。

## 排查客户端问题

老实说，我没怎么用过 FTP。每次您连接到 VSFTPD 服务器时，您应该在被动模式下这样做，这意味着服务器将在执行操作时打开另一个套接字，而不是初始化到您(客户端)的连接，如果您不使用被动 FTP 模式，您将从防火墙获得一个提示，以允许 FTP 客户端通过。要使用被动 FTP 连接，您应该运行:`ftp -p ftp_server_ip_or_alias`

当上传图像、pdf 或其他二进制文件时，你需要在你的 FTP 客户端发出**二进制**命令，在**上传**命令之前，否则事情会变得混乱，二进制文件会被破坏。

## 结论

我在安装和配置 VSFTPD 以及撰写本文的过程中获得了极大的乐趣。如果你还需要 VSFTPD 的帮助，我建议你访问 Arch Linux [wiki 页面](https://wiki.archlinux.org/index.php/Very_Secure_FTP_Daemon)，它包含了大量的信息。

感谢您的阅读，祝您有美好的一天！

[推特](https://twitter.com/denisnutiu) | [多福拉](https://m.do.co/c/22f012126c25)