# 在 AWS RDS 上删除 Postgres 数据库

> 原文：<https://dev.to/tathagata/ecto-drop-postgres-database-on-aws-rds-17gn>

假设在你的开发周期中，你搞砸了你的 postgres 数据库，摆脱混乱的最简单的方法是丢弃数据库并重新启动。在这个例子中，我们将看一个使用 Postgres 数据库的 [Phoenix](https://phoenixframework.org/) 项目。

### 警告

明确地说，如果您没有拍摄快照，删除数据库是一个坏主意。删除会删除所有数据；所以只在非 prod 中做。

### 问题:由于永久 rdsadmin，删除数据库失败

```
mix do ecto.drop, ecto.create, ecto.migrate 
```

这在您的本地 postgres 实例上运行良好。然而，如果数据库在 AWS RDS 上，你会看到这条有趣的消息。

```
$ mix ecto.drop
** (Mix) The database for SuperPetStore.Repo couldn't be dropped, reason given: ERROR:  55006: database "petdb" is being accessed by other users
DETAIL:  There is 1 other session using the database.
LOCATION:  dropdb, dbcommands.c:840 
```

如果你看看连接数

```
> select * from pg_stat_activity; 
```

果然，会有一个来自`rdsadmin`的持久连接。AWS RDS 用户负责备份、更新和维护。这种连接妨碍了丢弃数据库并重新开始。

```
> use postgres;
You are now connected to database "postgres" as user "petuser"
Time: 0.078s
postgres> drop database petdb;
You're about to run a destructive command.
Do you want to proceed? (y/n): y
Your call!
database "petdb" is being accessed by other users
DETAIL:  There is 1 other session using the database.

Time: 5.031s (5 seconds), executed in: 5.030s (5 seconds)
postgres> 
```

### [T1】pg _ termin ate _ back end](#pgterminatebackend)

```
select pg_terminate_backend(pid) from pg_stat_activity where datname='petdb';drop database pipeops;
You're about to run a destructive command.
Do you want to proceed? (y/n): y
Your call!
+------------------------+
| pg_terminate_backend   |
|------------------------|
| True                   |
+------------------------+
SELECT 1
DROP DATABASE
Time: 0.118s 
```

首先`pg_terminate_backend`允许你终止`rdsadmin`账户，在`rdsadmin`出现并连接到`petdb`之前，推入同一行的`drop`语句就完成了任务。

随着数据库的消失，您现在可以运行 ecto 来重新创建 db！

### 等待。上图和这个帖子有什么关系？

一件都没有。这是我在锡耶纳拍的照片。❤️，迫不及待想回去了！