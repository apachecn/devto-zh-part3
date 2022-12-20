# MariaDB:在命令行上备份/恢复数据库

> 原文：<https://dev.to/nabbisen/mariadb-backing-uprestoring-databases-on-the-command-line-351j>

### 总结

备份/恢复数据库是服务开发和管理的好伙伴。
这篇文章是关于如何在命令行上即时备份/恢复 [MariaDB](https://mariadb.org/) 数据库，基于 [MariaDB Corporation](https://mariadb.com/) 的[“备份和恢复概述”](https://mariadb.com/kb/en/library/backup-and-restore-overview/)。

#### 环境

*   MariaDB 10.0

<center>✿ ✿ ✿</center>

### 备份

就`mysqldump ... > file path` :

```
$  mysqldump -u %user% -p %from-database% > ./database-backup.sql 
```

Enter fullscreen mode Exit fullscreen mode

你会被询问密码。

*如果您想使用一行程序，将`-p`转换为`-p%password%`将是一种方式。(请注意可能包含密码的命令历史记录。)*

#### 参考文献

*   [备份工具- mysqldump](https://mariadb.com/kb/en/library/backup-and-restore-overview/#mysqldump)

### 恢复

就`mysql ... < file path` :

```
$  mysql -u %user% -p %to-database% < ./database-backup.sql 
```

Enter fullscreen mode Exit fullscreen mode

你会被询问密码。

*如果您想使用一行程序，将`-p`转换为`-p%password%`将是一种方式。(请注意可能包含密码的命令历史记录。)*

#### 部分恢复

可以通过创建一个临时用户来恢复一些表或记录。

* * *

这是一个通过使用名为`admin_restore_temp`的临时用户只恢复`db1`中的`table1`的例子。
首先执行 [GRANT](https://mariadb.com/kb/en/grant/) 让`admin_restore_temp`只拥有`table1`的所有权限:

```
GRANT SELECT
ON db1.* TO 'admin_restore_temp'@'localhost' 
IDENTIFIED BY 'its_pwd';

GRANT ALL ON db1.table1
TO 'admin_restore_temp'@'localhost'; 
```

Enter fullscreen mode Exit fullscreen mode

然后，使用备份文件、`/data/backup/db1.sql`和`--force`选项进行恢复，如下:

```
$  mysql -u admin_restore_temp -p --force < /data/backup/db1.sql 
```

Enter fullscreen mode Exit fullscreen mode

MariaDB 产生错误后，只会恢复`table1`。

* * *

有几种不同的方法。
例如，我们可以在创建一个临时数据库后使用`INSERT`语句恢复一些表中的部分记录，而不是使用`mysql ... < file path`命令。

#### 参考文献

*   [从转储文件中恢复数据](https://mariadb.com/kb/en/library/restoring-%0Adata-from-dump-files/)

<center>✿ ✿ ✿</center>

快乐服务🌲