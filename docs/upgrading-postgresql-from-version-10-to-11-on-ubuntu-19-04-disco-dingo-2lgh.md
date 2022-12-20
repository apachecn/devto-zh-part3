# 在 Ubuntu 19.04 (Disco Dingo)上将 PostgreSQL 从版本 10 升级到版本 11

> 原文：<https://dev.to/pauloxnet/upgrading-postgresql-from-version-10-to-11-on-ubuntu-19-04-disco-dingo-2lgh>

> 如何在 Ubuntu 上将 PostgreSQL 从 18.10 版升级到 19.04 版后，将其从 10 版升级到 11 版。

# TL；速度三角形定位法(dead reckoning)

升级到 Ubuntu 19.04 后:

```
$  sudo pg_dropcluster 11 main --stop
$  sudo pg_upgradecluster 10 main
$  sudo pg_dropcluster 10 main 
```

Enter fullscreen mode Exit fullscreen mode

# 升级 PostgreSQL

在 Ubuntu 升级到 19.04 期间，您会收到这条消息“配置 postgresql-common”:

> 过时的主要版本 10
> 
> PostgreSQL 版本 10 已过时，但服务器或客户端软件包仍会安装。
> 请安装最新的软件包(postgresql-11 和 postgresql-client-11)并使用 pg_upgradecluster 升级现有集群(请参见联机帮助页)。
> 
> 请注意，postgresql-11 的安装将自动创建一个默认的集群 11/main。
> 如果要升级 10/main 集群，需要删除已经存在的 11 集群(pg_dropcluster - stop 11 main，详见手册页)。
> 
> 不再支持旧的服务器和客户端软件包。
> 升级现有集群后，应删除 postgresql-10 和 postgresql-client-10 软件包。
> 
> 请参见/usr/share/doc/PostgreSQL-common/README。详情请见 Debian.gz。

使用`dpkg -l | grep postgresql`检查安装了哪些版本的 postgres:

```
ii  postgresql                                 11+199                               all          object-relational SQL database (supported version)
ii  postgresql-10                              10.8-0ubuntu0.18.10.1                amd64        object-relational SQL database, version 10 server
ii  postgresql-11                              11.3-0ubuntu0.19.04.1                amd64        object-relational SQL database, version 11 server
ii  postgresql-client                          11+199                               all          front-end programs for PostgreSQL (supported version)
ii  postgresql-client-10                       10.8-0ubuntu0.18.10.1                amd64        front-end programs for PostgreSQL 10
ii  postgresql-client-11                       11.3-0ubuntu0.19.04.1                amd64        front-end programs for PostgreSQL 11
ii  postgresql-client-common                   199                                  all          manager for multiple PostgreSQL client versions
ii  postgresql-common                          199                                  all          PostgreSQL database-cluster manager 
```

Enter fullscreen mode Exit fullscreen mode

运行`pg_lsclusters`，你的 10 和 11 主集群应该“在线”。

```
Ver Cluster Port Status Owner    Data directory               Log file
10  main    5432 online postgres /var/lib/postgresql/10/main /var/log/postgresql/postgresql-10-main.log
11  main    5433 online postgres /var/lib/postgresql/11/main /var/log/postgresql/postgresql-11-main.log 
```

Enter fullscreen mode Exit fullscreen mode

11 已经有一个集群“main ”(因为这是默认情况下在软件包安装时创建的)。这样做是为了让全新的安装开箱即用，而不需要先创建集群，但是当 11/main 也存在时，当您尝试升级 10/main 时，当然会发生冲突。推荐的程序是用`pg_dropcluster`移除 11 个集群，然后用`pg_upgradecluster`升级。

停止 11 集群并丢弃它。

```
$  sudo pg_dropcluster 11 main --stop 
```

Enter fullscreen mode Exit fullscreen mode

将 10 集群升级到最新版本。

```
$  sudo pg_upgradecluster 10 main 
```

Enter fullscreen mode Exit fullscreen mode

您的 10 个集群现在应该“关闭”，您可以验证运行`pg_lsclusters`

```
Ver Cluster Port Status Owner    Data directory              Log file
10  main    5433 down   postgres /var/lib/postgresql/10/main /var/log/postgresql/postgresql-10-main.log
11  main    5432 online postgres /var/lib/postgresql/11/main /var/log/postgresql/postgresql-11-main.log 
```

Enter fullscreen mode Exit fullscreen mode

检查升级后的集群是否正常工作，然后删除 10 集群。

```
$  sudo pg_dropcluster 10 main 
```

Enter fullscreen mode Exit fullscreen mode

完成所有数据检查后，您可以删除旧包。

```
$  sudo apt purge \
postgresql-10 postgresql-client-10 postgresql-contrib-10 
```

Enter fullscreen mode Exit fullscreen mode

# 免责声明。

在适用法律允许的范围内，本程序不提供任何担保。除非另有书面声明，否则版权所有者和/或其他方“按原样”提供程序，不提供任何形式的明示或暗示担保，包括但不限于对适销性和特定用途适用性的暗示担保。程序质量和性能的全部风险由您承担。如果程序被证明有缺陷，您将承担所有必要的服务、维修或修正的费用。

# 引用

最初发表于[www.paulox.net](https://www.paulox.net/2019/05/28/upgrading-postgresql-from-version-10-to-11-on-ubuntu-19-04-disco-dingo/)