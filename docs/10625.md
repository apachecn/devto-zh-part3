# 在 Ubuntu 18.04 上将 PostgreSQL 从 9.6 升级到 10.0

> 原文：<https://dev.to/pauloxnet/upgrading--postgresql-from-96-to-100-on--ubuntu-1804-49kl>

> 如何在 Ubuntu 上将 PostgreSQL 从 9.6 升级到 10.0？

# TL；速度三角形定位法(dead reckoning)

升级到 Ubuntu 18.04 后:

```
$  sudo pg_dropcluster 10.0 main --stop
$  sudo pg_upgradecluster 9.6 main
$  sudo pg_dropcluster 9.6 main 
```

Enter fullscreen mode Exit fullscreen mode

# 升级 PostgreSQL

在 Ubuntu 升级到 18.04 期间，您会收到这条消息“配置 postgresql-common”:

> 过时的主要版本 9.6
> 
> PostgreSQL 版已过时，但服务器或客户端软件包仍会安装。
> 请安装最新的软件包(postgresql-10 和 postgresql-client-10)并使用 pg_upgradecluster 升级现有集群(请参见联机帮助页)。
> 
> 请注意，postgresql-10 的安装将自动创建一个默认的集群 10.0/main。
> 如果要升级 9.6/main 集群，需要删除已经存在的 10 集群(pg_dropcluster - stop 10 main，详见手册页)。
> 
> 不再支持旧的服务器和客户端软件包。
> 升级现有集群后，应删除 postgresql-9.6 和 postgresql-client-9.6 软件包。
> 
> 请参见/usr/share/doc/PostgreSQL-common/README。详情请见 Debian.gz。

使用`dpkg -l | grep postgresql`检查安装了哪些版本的 postgres:

```
ii  postgresql                                 10+190                                      all          object-relational SQL database (supported version)
ii  postgresql-10                              10.3-1                                      amd64        object-relational SQL database, version 10 server
ii  postgresql-10-postgis-2.4                  2.4.3+dfsg-4                                amd64        Geographic objects support for PostgreSQL 10
ii  postgresql-10-postgis-2.4-scripts          2.4.3+dfsg-4                                all          Geographic objects support for PostgreSQL 10 -- SQL scripts
ii  postgresql-9.6                             9.6.8-0ubuntu0.17.10                        amd64        object-relational SQL database, version 9.6 server
ii  postgresql-9.6-postgis-2.3                 2.3.3+dfsg-1                                amd64        Geographic objects support for PostgreSQL 9.6
ii  postgresql-client                          10+190                                      all          front-end programs for PostgreSQL (supported version)
ii  postgresql-client-10                       10.3-1                                      amd64        front-end programs for PostgreSQL 10
ii  postgresql-client-9.6                      9.6.8-0ubuntu0.17.10                        amd64        front-end programs for PostgreSQL 9.6
ii  postgresql-client-common                   190                                         all          manager for multiple PostgreSQL client versions
ii  postgresql-common                          190                                         all          PostgreSQL database-cluster manager
ii  postgresql-contrib                         10+190                                      all          additional facilities for PostgreSQL (supported version)
ii  postgresql-contrib-9.6                     9.6.8-0ubuntu0.17.10                        amd64        additional facilities for PostgreSQL
ii  postgresql-plpython3-9.6                   9.6.8-0ubuntu0.17.10                        amd64        PL/Python 3 procedural language for PostgreSQL 9.6 
```

Enter fullscreen mode Exit fullscreen mode

运行`pg_lsclusters`，你的 9.6 和 10 主集群应该是“在线”的。

```
Ver Cluster Port Status Owner    Data directory               Log file
9.6 main    5432 down   postgres /var/lib/postgresql/9.6/main pg_log/postgresql-%Y-%m-%d.log
10  main    5433 down   postgres /var/lib/postgresql/10/main  /var/log/postgresql/postgresql-10-main.log 
```

Enter fullscreen mode Exit fullscreen mode

10 已经有一个集群“main ”(因为这是默认情况下在软件包安装时创建的)。这样做是为了让全新的安装开箱即用，而不需要首先创建集群，但是当您尝试升级 9.6/main 而 10/main 也存在时，当然会发生冲突。推荐的程序是用`pg_dropcluster`移除 10 集群，然后用`pg_upgradecluster`升级。

停止 10 集群并丢弃它。

```
$  sudo pg_dropcluster 10 main --stop 
```

Enter fullscreen mode Exit fullscreen mode

将 9.6 集群升级到最新版本。

```
$  sudo pg_upgradecluster 9.6 main 
```

Enter fullscreen mode Exit fullscreen mode

您的 9.6 集群现在应该“关闭”，您可以验证运行`pg_lsclusters`

```
Ver Cluster Port Status Owner    Data directory               Log file
9.6 main    5433 down   postgres /var/lib/postgresql/9.6/main ...
10  main    5432 online postgres /var/lib/postgresql/10/main  ... 
```

Enter fullscreen mode Exit fullscreen mode

检查升级后的集群是否正常工作，然后删除 9.6 集群。

```
$  sudo pg_dropcluster 9.6 main 
```

Enter fullscreen mode Exit fullscreen mode

完成所有数据检查后，您可以删除旧包。

```
$  sudo apt purge postgresql-9.6 postgresql-client-9.6 postgresql-contrib-9.6 
```

Enter fullscreen mode Exit fullscreen mode

# 免责声明。

在适用法律允许的范围内，本程序不提供任何担保。除非另有书面声明，否则版权所有者和/或其他方“按原样”提供程序，不提供任何形式的明示或暗示担保，包括但不限于对适销性和特定用途适用性的暗示担保。程序质量和性能的全部风险由您承担。如果程序被证明有缺陷，您将承担所有必要的服务、维修或修正的费用。

# 引用

最初发表于[www.paulox.net](https://www.paulox.net/2018/05/19/upgrading-postgresql-from-9-6-to-10-0-on-ubuntu-18-04/)