# MySQL 高可用性框架讲解–第三部分:故障转移场景

> 原文：<https://dev.to/scalegrid/mysql-high-availability-framework-explained-part-iii-failover-scenarios-a1h>

[![MySQL High Availability Framework Explained – Part III: Failover Scenarios | ScaleGrid Blog](img/47ceb3932f6335b6fb6584406629db6f.png)](https://scalegrid.io/blog/mysql-high-availability-framework-explained-part-3/)

在这个由三部分组成的博客系列中，我们在第一部分介绍了用于 [MySQL 托管](https://scalegrid.io/mysql.html "Fully managed MySQL hosting")的[高可用性(HA)框架](https://scalegrid.io/blog/mysql-high-availability-framework-explained-part-1/)，并在第二部分讨论了 [MySQL 半同步复制](https://scalegrid.io/blog/mysql-high-availability-framework-explained-part-2/)的细节。现在在第三部分，我们回顾框架如何处理一些重要的 [MySQL 故障场景](https://scalegrid.io/blog/mysql-high-availability-framework-explained-part-3/)并恢复以确保高可用性。

## MySQL 故障转移场景

### 场景 1 -主 MySQL 关闭

*   [Corosync](https://clusterlabs.org/corosync.html) 和 [Pacemaker](https://clusterlabs.org/pacemaker/) 框架检测到主 MySQL 不再可用。Pacemaker 会将主资源降级，并尝试通过重启 MySQL 服务进行恢复，如果可能的话。
*   此时，由于复制的[半同步特性，主设备上提交的所有事务已经被至少一个从设备接收。](https://scalegrid.io/blog/data-integrity-and-performance-considerations-in-mysql-semisynchronous-replication/)
*   Pacemaker 会等待，直到所有接收到的事务都应用到从属服务器上，并让从属服务器报告它们的提升分数。分数计算的方式是，如果从机与主机完全同步，分数为“0”，否则为负数。
*   Pacemaker 选择报告得分为 0 的从服务器，并提升该从服务器，该从服务器现在承担主 MySQL 的角色，在该角色上允许写入。
*   在从属升级之后，[资源代理](https://clusterlabs.org/components.html)触发 DNS 重新路由模块。该模块用新主机的 IP 地址更新代理 DNS 条目，因此，便于所有应用写入被重定向到新主机。
*   Pacemaker 还设置可用的从设备，以便从这个新的主设备开始复制。

因此，无论何时主 MySQL 关闭(无论是由于 MySQL 崩溃、操作系统崩溃、系统重启等等)。)，我们的 HA 框架检测到它并提升一个合适的从机来接管主机的角色。这确保了应用程序可以继续使用系统。

### 场景 2——从属 MySQL 关闭

*   Corosync 和 Pacemaker 框架检测到从属 MySQL 不再可用。
*   Pacemaker 试图通过在节点上重启 MySQL 来恢复资源。如果启动，它将作为从属主机添加回当前主机，复制将继续。
*   如果恢复失败，Pacemaker 会将该资源报告为关闭——基于此可以生成警报或通知。如有必要，ScaleGrid [支持团队](mailto:support@scalegrid.io)将处理该节点的恢复。
*   在这种情况下，对 MySQL 服务的可用性没有影响。

### 场景 3 -网络分区-主节点和从节点之间的网络连接中断

这是任何分布式系统中的一个经典问题，其中每个节点都认为其他节点出现故障，而实际上，只有节点之间的网络通信中断。这种情况通常被称为裂脑情况，如果处理不当，可能会导致多个节点声称自己是主 MySQL，从而导致数据不一致和损坏。

让我们用一个例子来回顾我们的框架如何处理集群中的裂脑场景。我们假设由于网络问题，集群已经划分为两个组——一个组中的主设备和另一个组中的两个从设备，我们将其表示为[(M)，(S1，S2)]。

*   Corosync 检测到主节点无法与从节点通信，而从节点可以相互通信，但不能与主节点通信。
*   主节点将不能提交任何事务，因为半同步复制期望在主节点可以提交之前来自至少一个从节点的确认。同时，Pacemaker 会根据 Pacemaker 设置“no-quorum-policy = stop”关闭主节点上的 MySQL，因为缺少仲裁。这里的 Quorum 是指大多数节点，或者在 3 节点集群设置中的三分之二。因为在集群的这个分区中只有一个主节点在运行，所以触发了无仲裁策略设置，导致 MySQL 主节点关闭。
*   现在，分区[(S1)，(S2)]上的 Pacemaker 检测到集群中没有可用的主节点，并启动升级过程。假设 S1 与主节点保持最新状态(如半同步复制所保证的那样)，则它会被提升为新的主节点。
*   应用流量将被重定向到这个新的主 MySQL 节点，从 S2 将开始从新的主节点进行复制。

因此，我们看到 MySQL HA 框架有效地处理了裂脑情况，确保了主节点和从节点之间的网络连接中断时的数据一致性和可用性。

我们关于使用半同步复制和[Corosync](https://clusterlabs.org/corosync.html)plus[Pacemaker](https://clusterlabs.org/pacemaker/)栈的 MySQL 高可用性(HA)框架的 3 部分博客系列到此结束。在 ScaleGrid，我们为 AWS 上的[MySQL](https://scalegrid.io/mysql/aws.html)和 Azure 上的[MySQL](https://scalegrid.io/mysql/azure.html)提供高可用性托管，这是基于本博客系列中解释的概念实现的。请访问 [ScaleGrid 控制台](https://console.scalegrid.io/users/register)免费试用我们的解决方案。