# Debian 上的 Microsoft SQL Server 2017

> 原文：<https://dev.to/nabbisen/microsoft-sql-server-2017-on-debian-n77>

**封面图片由[贾莫鲁克](https://pixabay.com/users/jarmoluk-143740/)原创，经过精心编辑。*

* * *

## 简介

[微软](https://www.microsoft.com/) [SQL Server](https://www.microsoft.com/sql-server) 是一种关系数据库管理系统，又名 RDBMS。
从 SQL Server 2017 开始在 [Linux](https://www.linux.org/) 上可用，既有安装，也有 [Docker](https://www.docker.com/) 镜像。
这篇文章展示了如何在[Debian](https://www.debian.org/)9 "[stretch](https://www.debian.org/releases/stretch/)"上安装它。

## 教程

这个帖子是基于[官方指导](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup?view=sql-server-2017)的。

#### 要求

**2GB RAM** 绝对需要，以便在安装软件包后运行安装脚本。
其他人是[这里是](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup?view=sql-server-2017#system)。

#### 准备

[`sudo`](https://www.sudo.ws/) 命令在运行安装脚本时也是必要的。
因此，安装并设置:

```
#  apt-get install sudo
#  visudo 
```

安装其他必要的软件包作为准备。

```
$  sudo apt-get install curl 
```

添加`mssql-server`的依赖项。
首先安装安全包:

```
$  sudo apt-get install apt-transport-https 
```

接下来，安装旧的 [libssl](https://wiki.openssl.org/index.php/Libssl_API) 。
编辑`/etc/apt/sources.list`在底部添加一行如下:

```
+ deb http://ftp.debian.org/debian jessie main 
```

然后安装:

```
$  sudo apt-get update
$  sudo apt-get install libssl1.0 
```

而且，为了`apt-key add microsoft.asc` :

```
$  sudo apt-get install gnupg 
```

#### 安装

我们准备好了。
让我们添加存储库:

```
$  curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
$  curl https://packages.microsoft.com/config/ubuntu/16.04/mssql-server-2017.list | sudo tee /etc/apt/sources.list.d/mssql-server.list 
```

然后安装:

```
$  sudo apt-get update
$  sudo apt-get install mssql-server 
```

运行设置脚本:

```
$  sudo /opt/mssql/bin/mssql-conf setup 
```

你可以按照安装程序。
“速成”版免费使用[有限制](https://www.microsoft.com/en-us/sql-server/sql-server-2017-editions#CP_AccordionWithTable)。

最后，重启服务:

```
$  sudo systemctl restart mssql-server.service 
```

## 结论

官方指导显示[RHEL 7.3 ~](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-red-hat?view=sql-server-2017)/[Suse Linux](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-suse?view=sql-server-2017)/[Ubuntu 16.04](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-2017)快速入门。
本帖教程是 [Ubuntu](https://www.ubuntu.com/) 的一个亚种
正如我在引言中所写的， [Docker 图片也有](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-2017&pivots=cs1-bash)。

在我做工程师的早期，我没有想到 SQL Server 会在 Linux 上自由使用，即使有一些限制...🙂

感谢您的阅读。
快乐计算。