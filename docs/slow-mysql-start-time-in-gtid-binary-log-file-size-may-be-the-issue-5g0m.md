# GTID 中 MySQL 启动时间慢？二进制日志文件大小可能是问题所在

> 原文：<https://dev.to/scalegrid/slow-mysql-start-time-in-gtid-binary-log-file-size-may-be-the-issue-5g0m>

[![](img/03b636e04df503d2cfb8f4c2a4136ab3.png)](https://scalegrid.io/blog/slow-mysql-start-time-in-gtid-binary-log-file-size-may-be-the-issue/)

你是否在 GTID 模式下遇到过 MySQL 启动缓慢的问题？我们最近在一次 MySQL 托管部署中遇到了这个问题，并着手解决这个问题。在这篇博客中，我们分解了可能是[减缓 MySQL 重启时间](https://scalegrid.io/blog/slow-mysql-start-time-in-gtid-binary-log-file-size-may-be-the-issue/ "MySQL Hosting")的问题，如何为您的部署进行调试，以及您可以做些什么来减少启动时间并提高您对[基于 GTID 的复制的理解](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html)。

## 我们是如何发现问题的

我们在低端的基于磁盘的 MySQL 5.7.21 部署上调查了 MySQL 启动时间缓慢的问题，该部署启用了 GTID 模式。该系统是[主从](https://dev.mysql.com/doc/refman/8.0/en/replication.html)对的一部分，处于中等写入负载下。在定期维护期间重新启动时，我们注意到数据库服务器需要 5-10 分钟才能启动并开始接受连接。那种拖延没有意义，所以我们着手调查。

## 调试 MySQL 缓慢的启动时间

我们使用流行的工具 pt-ioprofile 来查看数据库正在做什么。pt-ioprofile 是 Percona 的[流行工具包](https://www.percona.com/doc/percona-toolkit/LATEST/index.html)中一个非常重要的工具，用于调试 MySQL 问题，你可以在他们的[文档](https://www.percona.com/doc/percona-toolkit/LATEST/pt-ioprofile.html)中看到完整的特性列表。pt-ioprofile 工具使用 strace 和 lsof 来观察进程的 I/O，并打印出文件和 I/O 活动的表格。

因此，我们启动 MySQL，等待 mysqld 进程产生，并启动 pt-ioprofile 来查看问题可能是什么:

```
# pt-ioprofile --profile-process mysqld --run-time 200
Tue Oct 9 15:42:24 UTC 2018
Tracing process ID 18677
total      pread       read     pwrite      write      fsync  fdatasync       open      close   getdents      lseek      fcntl filename
...
216.550641   0.000000  216.550565   0.000000   0.000000   0.000000   0.000000   0.000015   0.000040   0.000000   0.000021   0.000000 /mysql_data/binlogs/mysql-bin.000014
...

```

### 是什么让你的 MySQL 重启变慢了？

多次运行后，我们观察到以下情况:

*   mysqld 进程大部分时间都在读取最新的二进制日志文件。即使服务器已经正常停止，并且不需要[崩溃恢复](https://dev.mysql.com/doc/refman/5.7/en/innodb-recovery.html#innodb-crash-recovery)等，情况也是如此。
*   服务器也花费了相当多的时间来加载 [InnoDB 数据文件](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html)，但是与读取最新的二进制日志文件所花费的时间相比，这个时间要小得多。
*   如果立即重新启动服务器，后续的重新启动会快得多。
*   因为数据库关闭会刷新二进制日志并在启动时创建一个新的日志，所以我们做了一个额外的实验——在关闭服务器之前，我们[刷新了二进制日志](https://dev.mysql.com/doc/refman/8.0/en/flush.html)。随后的服务器启动又很快。

se 的观察清楚地指出了 MySQL 花费大量时间读取最新的二进制日志文件的事实。如果文件很小，就像在关闭之前刷新日志文件一样，启动会很快。

## 了解 Binlog GTID 恢复

事实证明，为了填充 [gtid_executed](https://dev.mysql.com/doc/refman/8.0/en/replication-options-gtids.html#sysvar_gtid_executed) 和 [gtid_purged](https://dev.mysql.com/doc/refman/8.0/en/replication-options-gtids.html#sysvar_gtid_purged) 的值，MySQL 服务器必须解析二进制日志文件。

以下是 MySQL 5.7 [文档](https://dev.mysql.com/doc/refman/5.7/en/replication-options-gtids.html#sysvar_binlog_gtid_simple_recovery)方法建议的摘要，基于一个错误或正确的解读:

#### 当**binlog _ gtid _ simple _ recovery**= FALSE 时:

计算 **gtid_executed:**

*   从最新的二进制日志文件开始迭代，在具有 **Previous_gtids_log_event** 条目的第一个文件处停止。
*   从该二进制日志文件中消耗来自 **Previous_gtids_log_event** 和 **GTID_log_events** 的所有 Gtid，并将该 Gtid 集合存储在内部。它被称为 *gtids_in_binlog。*
*   **gtid_executed** 的值计算为*gtid _ in _ binlog*和 **mysql.gtid_executed 表**中 gtid 的并集。

*如果有大量没有 gtid 的二进制日志文件，例如在 **gtid_mode** =OFF 时创建的，这个过程会非常耗时。*

类似地，要计算 **gtid_purged:**

*   从最旧到最新迭代二进制日志文件，在包含非空 **Previous_gtids_log_event** (至少有一个 GTID)或至少有一个 **Gtid_log_event** 的第一个二进制日志处停止。
*   从该文件中读取 **Previous_gtids_log_event** 。计算内部变量*gtids _ in _ bin log _ not _ purged*，从 *gtids_in_binlog 中减去该 GTID 集合。*
*   **gtid_purged** 的值被设置为 **gtid_executed** ，减去*gtid _ in _ bin log _ not _ purged*。

所以，这是我们理解旧版本中事物如何工作的基础。然而，当**binlog _ gtid _ simple _ recovery**为真时，可以进行某些优化。这是我们感兴趣的情况:

#### 当**binlog _ gtid _ simple _ recovery**=真时:

(注意，这是 MySQL 5.7.7 及更高版本中的默认设置)

*   只读取最旧和最新的二进制日志文件。
*   从最旧的二进制日志文件中找到的 **Previous_gtids_log_event** 或 **Gtid_log_event** 计算 **gtid_purged** 。
*   从最新二进制日志文件中找到的 **Previous_gtids_log_event** 或 **Gtid_log_event** 计算 **gtid_executed** 。
*   因此，在服务器重启期间或清除二进制日志时，仅读取两个二进制日志文件。

所以，对于 MySQL 版本 5.7.7 及以上，系统启动时总是会读取最新和旧的二进制日志文件，以正确初始化 [GTID 系统变量](https://dev.mysql.com/doc/refman/5.7/en/replication-options-gtids.html#replication-sysvars-gtids)。读取最旧的二进制日志文件并不昂贵，因为 MySQL 正在寻找的[事件](https://dev.mysql.com/doc/internals/en/binlog-event.html)、 [Previous_gtids_log_event](https://dev.mysql.com/doc/dev/mysql-server/8.0.2/classPrevious__gtids__log__event.html) 总是二进制日志文件中的第一个事件。

但是，为了正确计算 gtid_executed，服务器必须通读整个最新的二进制日志文件，并收集该文件中的所有事件。**因此，系统启动时间与最新二进制日志文件**的大小成正比。

注意，当[binlog _ gtid _ simple _ recovery](https://dev.mysql.com/doc/refman/5.7/en/replication-options-gtids.html#sysvar_binlog_gtid_simple_recovery)为假时，情况甚至更糟。因为在最近的版本中它不再是默认选项，所以这不是一个大问题。

## 如何解决你开始时间慢的问题

理解了我们遇到的问题的原因后，我们决定的解决方案是相当明显的- [减少二进制日志文件的大小](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_max_binlog_size)。二进制日志文件的默认大小是 1GB。在启动时解析这样大小的文件需要时间，因此将 [max_binlog_size](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_max_binlog_size) 的值减小到一个较低的值是有意义的。

如果无法减小二进制日志文件的大小，那么在 mysqld 进程维护性关闭之前刷新二进制日志文件有助于减少 binlog GTID 恢复时间。