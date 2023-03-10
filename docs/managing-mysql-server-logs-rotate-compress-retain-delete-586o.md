# 管理 MySQL 服务器日志:旋转、压缩、保留和删除

> 原文：<https://dev.to/scalegrid/managing-mysql-server-logs-rotate-compress-retain-delete-586o>

[![](img/be0c8030abe5024c3f137459ceed3e7f.png)](https://scalegrid.io/blog/managing-mysql-server-logs-rotate-compress-retain-delete/)

MySQL 服务器生成几个[日志](https://dev.mysql.com/doc/refman/5.7/en/server-logs.html)，可以帮助你监控服务器的活动。但是，一旦启用了这些日志，它们的大小就会增加，并开始占用过多的磁盘空间。这就是为什么有一种自动的方法来归档和保存 MySQL 日志文件一段时间，以及删除旧的日志文件是很重要的。在这篇博文中，我们描述了为您的 [MySQL 部署](https://scalegrid.io/mysql.html)设置和[管理 MySQL 错误日志、一般日志和慢速查询日志](https://scalegrid.io/blog/managing-mysql-server-logs-rotate-compress-retain-delete/)的一些最佳实践。

## 设置 MySQL 服务器日志记录

让我们看看如何设置以下 3 种类型的日志:

### 错误日志

记录启动、运行或停止 mysqld 时遇到的所有问题。可以通过在/etc/my.cnf 文件中使用以下选项来启用该日志:

*   log_error=/var/log/mysql/mysqld.log

### 常规查询日志

记录已建立的客户端连接和从客户端接收的语句。可以通过在/etc/my.cnf 文件中使用以下选项来启用该日志:

*   general_log=ON
*   通用日志文件=/var/log/mysql/general.log

### 慢速查询日志

记录执行时间超过 long_query_time 秒的查询。可以通过/etc/my.cnf 文件中的以下选项来启用该日志:

*   slow_query_log=ON
*   slow _ query _ log _ file =/var/log/MySQL/MySQL-slow query . log

## 设置日志轮转的标准

作为一个例子，让我们有一些管理一般 MySQL 查询日志的标准。通过询问以下问题，我们可以提出一组合适的日志管理标准:

问:日志文件可以增长的最大大小是多少？

答:假设它可以增长到 300 MB，然后需要旋转和压缩。

问:您希望日志文件循环的频率是多少？

答:我们可以说我们希望每天轮换日志。

问:您想保留多少旧的日志文件？

答:我们希望保留最后 30 个日志文件。

根据上述标准，一般查询日志管理所需的总磁盘空间约为 1.2 GB。假设压缩率为 90%——我们将有 30 个大小为 30 MB 的压缩日志文件和一个大约 300 MB 的活动日志文件。

## 使用 Linux logrotate 实用程序管理日志

[logrotate](https://linux.die.net/man/8/logrotate) 是一个 Linux 实用程序，有助于高效管理日志文件，并提供自动旋转、压缩和删除日志文件的选项。通过在/etc/logrotate.d 文件夹中创建一个配置文件，可以为 logrotate 实用程序配置上面建立的标准。

让我们将这个配置文件称为 mysqlgeneral，该文件的内容将是:

```
/var/log/mysql/general.log{
        compress
        dateext
        maxsize 300M
        copytruncate
        maxage 365
        dateformat -%Y%m%d%s
        daily
        rotate 30
        notifempty
}
```

使用上面的 logrotate 选项，一般查询日志可以每天循环一次，也可以在日志文件大小超过 300 MB 时循环一次。旧日志被压缩，30 个这样的文件将被保留。如果日志文件由于设置“notifempty”而为空，将跳过日志循环。

“copytruncate”选项用于确保当前日志文件在循环期间不会被删除，只有其内容会被截断。这一点很重要，因为一些应用程序希望日志文件始终可用，如果不先停止应用程序，就不可能删除日志。

既然已经为常规查询日志设置了日志循环配置，那么必须运行 logrotate 实用程序，以便执行上面的配置。这通常是通过 cron 作业完成的。我们可以将 logrotate 脚本放在/etc/cron.hourly 目录中，将其设置为每小时运行一次:

```
#!/bin/sh

/usr/sbin/logrotate /etc/logrotate.conf
EXITVALUE=$?
if [ $EXITVALUE != 0 ]; then
    /usr/bin/logger -t logrotate "ALERT exited abnormally with [$EXITVALUE]"
fi
exit 0 
```

因此，通过几个简单的步骤，我们已经根据我们的标准为 MySQL 常规日志设置了日志轮换。MySQL 错误日志和慢速查询日志也可以遵循相同的方法。查看其他帖子，了解更多关于优化 MySQL 部署的信息:

*   [计算 MySQL 服务器的 InnoDB 缓冲池大小](https://scalegrid.io/blog/calculating-innodb-buffer-pool-size-for-your-mysql-server/)
*   [MySQL 教程——在 MySQL 服务器上配置和管理 SSL](https://scalegrid.io/blog/configuring-and-managing-ssl-on-your-mysql-server/)
*   [MySQL 高可用性框架讲解–第一部分:简介](https://scalegrid.io/blog/mysql-high-availability-framework-explained-part-1/)