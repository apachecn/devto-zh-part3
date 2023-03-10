# 在 PostgreSQL 中管理高可用性—第二部分

> 原文：<https://dev.to/scalegrid/managing-high-availability-in-postgresql--part-ii-1djc>

[![](img/8ce217f81cd0b6a6bb340a734c4f2afa.png)](https://scalegrid.io/blog/managing-high-availability-in-postgresql-part-2/)

您是否正在云中部署 [PostgreSQL，并希望了解您实现高可用性的选项？在我们之前的博客文章](https://scalegrid.io/postgresql.html)[管理 PostgreSQL 中的高可用性-第一部分](https://scalegrid.io/blog/managing-high-availability-in-postgresql-part-1/)中，我们讨论了 ClusterLabs 的 PostgreSQL 自动故障转移(PAF)的功能和作用。在[的第二部分](https://scalegrid.io/blog/managing-high-availability-in-postgresql-part-2/)，我们将向您介绍另一种开源工具，来自 2ndQuadrant 的 Replication Manager，接下来的第三部分我们将深入探讨第三种替代工具，Zalando 的 Patroni。

## 复制管理器(repmgr)

repmgr 是由 2ndQuadrant 开发的开源工具套件，用于管理您的 [PostgreSQL](https://www.postgresql.org/) 集群的复制和故障转移。它提供了设置、配置、管理和监视 PostgreSQL 复制的工具，还使您能够使用 repmgr 实用程序执行手动切换和故障转移任务。这个免费工具支持并增强 PostgreSQL 内置的[流复制](https://scalegrid.io/blog/getting-started-with-postgresql-streaming-replication/ "Getting Started with PostgreSQL Streaming Replication")。

Replication Manager 提供了两个主要工具来管理 PostgreSQL 的复制和故障切换。

### repmgr

*   一个命令行界面实用程序，使您能够执行各种管理任务。
*   repmgr 使您能够设置备用服务器、提升备用服务器、执行切换以及监视 PostgreSQL 集群的状态。
*   它还为几乎所有的管理命令提供了模拟运行选项。

### repmgrd

这是守护程序，它:

*   主动监视 PostgreSQL 集群，并根据集群的状态执行必要的操作。
*   通过将最符合条件的备用节点提升为新的主节点，在主节点关闭时执行自动故障转移。
*   提供了监视和存储与复制性能相关的数据的选项。
*   通过调用注册事件的用户脚本来提供通知。

## 它是如何工作的

repmrg 不仅管理 PostgreSQL 集群的复制，还能够设置复制的备用服务器。在初始安装之后，我们需要使用每台服务器上所需的详细信息对 repmgr 配置文件(repmgr.conf)进行更改。配置服务器时，需要使用 repmgr 主/备用 register 命令向 repmgr 注册它。首先，设置并注册主节点。然后，使用 repmgr standby clone 命令创建和配置备用服务器，该命令从另一台 PostgreSQL 服务器克隆 PostgreSQL 备用节点。

Replication Manager 利用 PostgreSQL 扩展功能，并在群集数据库上创建自己的架构来存储与群集相关的信息。在使用 repmgr 注册主服务器的过程中，会安装扩展并创建模式。设置完成后，可以执行手动管理操作，如升级、跟踪、切换等。可以使用 repmgr 实用程序来完成。对于切换操作，需要在节点之间设置无密码 SSH。

可以使用 [repmgrd](https://repmgr.org/docs/4.0/repmgrd-basic-configuration.html) 设置自动故障转移。repmgrd 要求在启动 PostgreSQL 服务器时加载共享库“repmgr”。应该在 postgresql.conf 文件的 **shared_preload_libraries** 配置参数中提到库名。此外，要使 repmgrd 工作，需要在 repmgr.conf 文件中设置**故障转移=自动**参数。一旦设置了所有这些参数，repmgrd 守护进程就开始主动监视集群。如果主节点出现任何故障，它将尝试多次重新连接。当所有连接到主服务器的尝试都失败时，repmgrd 会选择最合适的备用服务器作为新的主服务器。

repmgr 还支持事件通知。它有一组预定义的事件，并将这些事件的每次出现存储在 repmgr.events 表中。repmgr 允许将事件通知传递给用户定义的程序或脚本，该程序或脚本可以采取进一步的操作，例如发送电子邮件或触发任何警报。这是通过在 repmgr.conf 中设置**event _ notification _ command**参数来完成的。

### 它如何处理大脑分裂的情况？

repmgr 使用**位置**参数来处理[裂脑场景](https://repmgr.org/docs/4.2/repmgrd-network-split.html)，其中每个节点都应该根据其所在的数据中心来指定位置参数。如果出现任何网络拆分，repmgr 将确保提升与主节点位于同一位置的节点。如果在该位置找不到任何节点，它将不会提升任何位置的任何节点。

如果集群中有偶数个服务器，它还可以处理网络隔离。这是通过一个名为[见证服务器](https://repmgr.org/docs/4.2/repmgr-concepts.html#WITNESS-SERVER)的额外节点来完成的。见证服务器是一个仅用于多数投票计数的节点。该服务器上不会安装 PostgreSQL，因此不会参与复制。

### 有什么设置要求吗？

*   repmgr 将需要一个专用数据库和一个拥有超级用户权限的用户。但是，如果您不愿意让超级用户访问 repmgr 用户，也可以选择提供超级用户。
*   如果希望 repmgr 复制位于 PostgreSQL 数据目录之外的配置文件，和/或测试切换功能，还需要两台服务器之间的无密码 SSH 连接，并且应该安装 rsync。
*   如果打算使用 pg_ctl(默认情况下由 repmgr 使用)以外的基于服务的命令来启动、停止、重新加载和重新启动，可以在 repmgr 配置文件(repmgr.conf)中指定它们。
*   The basic configuration parameters are required in rep mgr configuration file are as follows:

    | **node _ id (int)** -a unique integer greater than zero, which is used to identify the node. **Node _ name (string)** -It is recommended to use an arbitrary (but unique) string, using the host name of the server or another identifier explicitly related to the server to avoid confusion. **conninfo (string)** -database connection information as conninfo string. All servers in the cluster must be able to connect to the local node using this string. **data _ directory (string)** -the data directory of the node. Repmgr needs to use PostgreSQL instance to perform operations when it is not running and there is no other way to determine the data directory. |

## repmgr Pros

*   Repmgr 提供了帮助设置主节点和备用节点以及配置复制的实用程序。
*   它不使用任何额外的端口进行通信。如果您想执行切换，那么只需要配置无密码 SSH。
*   通过调用已注册事件的用户脚本来提供通知。
*   在主服务器出现故障时执行自动故障转移。

## repmgr Cons

*   repmgr 不会检测备用服务器在恢复配置中是否配置了未知或不存在的节点。即使该节点在没有连接到主/级联备用节点的情况下运行，它也会显示为备用节点。
*   无法从 PostgreSQL 服务关闭的节点中检索另一个节点的状态。因此，它不提供分布式控制解决方案。
*   它不处理单个节点的健康恢复。

## 高可用性测试场景

我们使用 repmgr 对 PostgreSQL 高可用性管理进行了一些测试。所有这些测试都是在应用程序运行并将数据插入 PostgreSQL 数据库时运行的。该应用程序是使用 PostgreSQL Java [JDBC 驱动程序](https://jdbc.postgresql.org/)编写的，利用了连接故障转移功能。

### 备用服务器测试

| Sl。不 | 测试场景 | 观察 |
| one | 终止 PostgreSQL 进程 | 备用服务器被标记为失败。writer 应用程序没有中断。需要手动干预才能再次启动 PostgreSQL 进程。 |
| Two | 停止 PostgreSQL 进程 | 备用服务器被标记为失败。writer 应用程序没有中断。需要手动干预才能再次启动 PostgreSQL 进程。 |
| three | 重新启动服务器 | 备用服务器被标记为失败。服务器重启后启动，PostgreSQL 被手动启动，服务器被标记为正在运行。writer 应用程序没有中断。 |
| four | 停止 repmgrd 进程 | 备用服务器将不会成为自动故障转移情况的一部分。发现 PostgreSQL 服务正在运行。writer 应用程序没有中断。 |

### 主/主服务器测试

| **Sl。否** | **测试场景** | **观察** |
| one | 终止 PostgreSQL 进程 | 

*   The MGRD starts the health check of the primary server connections on all standby servers at fixed intervals.
*   When all retries fail, an election is triggered on all standby servers. As a result of the election, the candidate who recently won the LSN Award was promoted.
*   The failed standby server will wait for the notification of the new primary node and follow it after receiving the notification.
*   Writer application is down. Manual intervention is required to start PostgreSQL process again.

 |
| Two | 停止 PostgreSQL 进程，并在运行状况检查到期后立即将其恢复 | 

*   The MGRD starts the health check of the primary server connections on all standby servers at fixed intervals.
*   When all retries fail, an election is triggered on all standby nodes. However, since the old primary server came back, the newly elected primary server did not inform the existing standby server.
*   The cluster is in an uncertain state and requires manual intervention.

 |
| three | 重新启动服务器 | 

*   When the health check of the primary connection on all standby servers fails, repmgrd starts the election.
*   Eligible candidates are promoted. When this server returns, it does not join the cluster and is marked as a failure.
*   Run the repmgr node rejoin command to add the server back to the cluster. Writer application is down.

 |
| four | 停止 repmgr 进程 | 

*   The primary server is not in an automatic failover situation.
*   PostgreSQL service was found to be running. Writer application is not interrupted.

 |

### 网络隔离测试

| **Sl。否** | **测试场景** | **观察** |
| one | 通过网络将主服务器与其他服务器隔离开来(在 repmgr 配置中，所有服务器的位置值都相同) | 

*   When the health check of the primary connection on all standby servers fails, repmgrd starts the election.
*   The qualified standby node is promoted, but PostgreSQL process is still running on the old primary node.
*   There are two nodes operating as master nodes. After network isolation is corrected, manual intervention is required.

 |
| Two | 通过网络将主服务器与其他服务器隔离开来(备用服务器具有相同的 location 值，但主服务器在 repmgr 配置中具有不同的 location 值) | 

*   When the health check of the primary connection on all standby servers fails, repmgrd starts the election.
*   However, a new primary server was not selected, because the location of the standby server is different from the primary server.
*   The repmgrd enters the degraded monitoring mode. PostgreSQL runs on all nodes, and there is only one master node in the cluster.

 |

## 推理

repmgr 提供了几个命令来设置和监视 PostgreSQL 复制。它功能丰富，还简化了数据库管理员(DBA)的工作。然而，它不是一个成熟的高可用性管理工具，因为它不会管理资源。需要手动干预来确保资源处于正确的状态。

因此，在这篇文章中，我们讨论了第二象限 Replication Manager 的功能和工作方式。在我们的下一篇文章中，我们将使用 Zalando 的 Patroni 讨论相同的高可用性方面。对于希望在云中实现高可用性自动化的用户，请查看我们的 [PostgreSQL on Azure](https://scalegrid.io/postgresql/azure.html) 和 [PostgreSQL on AWS](https://scalegrid.io/postgresql/aws.html) 完全托管解决方案。