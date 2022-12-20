# 在 OpenBSD 6.4 上安装 MariaDB 10.0 服务器

> 原文：<https://dev.to/nabbisen/installing-mariadb-server-on-openbsd-5lm>

这篇文章展示了如何在 [OpenBSD](https://www.openbsd.org/) 上安装 [MariaDB](https://mariadb.org/) 并设置它。
[MariaDB](https://mariadb.org/) 是一个开源软件，提供了一个很棒的关系数据库服务器。

作为一个 [MySQL](https://www.mysql.com/) 的民谣而出名。
我喜欢关于他们名字的故事。
[Michael " Monty " wide nius](https://en.wikipedia.org/wiki/Michael_Widenius)是 MySQL AB 的创始人之一，现在是 MariaDB Corporation AB 的 CTO。MariaDB 以他的小女儿 Maria 命名，MySQL 以他的大女儿 My 命名😄从安全维护的角度来看，我已经从 MySQL 迁移到了 MariaDB。

至于 [PostgreSQL](https://www.postgresql.org/) ，MariaDB 和 PostgreSQL 我都喜欢。
MariaDB 是多线程的，所以看起来很轻量级，PostgreSQL 是多线程的，所以看起来很健壮。
最近我似乎比 PostgreSQL 更频繁地使用 MariaDB，但情况并非总是如此。

<center>✿ ✿ ✿</center>

## 环境

*   操作系统:OpenBSD 6.4 amd64
*   数据库:MariaDB 10.0

<center>✿ ✿ ✿</center>

## 程序

* *注意*:在代码区，前导`#`表示由超级用户执行；相当于使用`doas`命令(作为 root)，而前面的`$`是指一般用户。

#### 安装包

```
#  pkg_add mariadb-server 
```

Enter fullscreen mode Exit fullscreen mode

启用称为“mysqld”的 MariaDB 守护进程:

```
#  rcctl enable mysqld
  #  # check it
#  cat /etc/rc.conf.local
... pkg_scripts=mysqld ... 
```

Enter fullscreen mode Exit fullscreen mode

#### 准备系统

```
#  mysql_install_db
Installing MariaDB/MySQL system tables in '/var/mysql' ... ...  PLEASE REMEMBER TO SET A PASSWORD FOR THE MariaDB root USER !
To do so, start the server, then issue the following commands:

'/usr/local/bin/mysqladmin' -u root password 'new-password'
'/usr/local/bin/mysqladmin' -u root -h [host] password 'new-password'

Alternatively you can run:
'/usr/local/bin/mysql_secure_installation'

which will also give you the option of removing the test
databases and anonymous user created by default.  This is
strongly recommended for production servers.

See the MariaDB Knowledgebase at http://mariadb.com/kb or the
MySQL manual for more instructions.

You can start the MariaDB daemon with:
/etc/rc.d/mysqld start

Please report any problems at http://mariadb.org/jira

The latest information about MariaDB is available at http://mariadb.org/.
You can find additional information about the MySQL part at:
http://dev.mysql.com
Consider joining MariaDB's strong and vibrant community:
https://mariadb.org/get-involved/ 
```

Enter fullscreen mode Exit fullscreen mode

#### 启动 MariaDB 服务器

```
#  rcctl start mysqld
mysqld(ok) 
```

Enter fullscreen mode Exit fullscreen mode

#### 初始化数据库

运行`mysql_secure_installation`，然后按照几个步骤回答问题。
下面的回答是我的例子。

```
#  mysql_secure_installation
 NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY! 
```

Enter fullscreen mode Exit fullscreen mode

问题#1(第一次点击回车即可):

```
In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on... 
```

Enter fullscreen mode Exit fullscreen mode

问题 2:

```
Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success! 
```

Enter fullscreen mode Exit fullscreen mode

问题 3:

```
By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success! 
```

Enter fullscreen mode Exit fullscreen mode

问题 4:

```
Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success! 
```

Enter fullscreen mode Exit fullscreen mode

问题 5:

```
By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success! 
```

Enter fullscreen mode Exit fullscreen mode

问题 6:

```
Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB! 
```

Enter fullscreen mode Exit fullscreen mode

#### (完了！)现在 DDL 和 DML 都有了

为了作为客户端访问 MariaDB 服务器，可以使用 [`mysql`命令](https://mariadb.com/kb/en/library/mysql-command-line-client/)和 MySQL:

```
$  mysql -u root -p 
```

Enter fullscreen mode Exit fullscreen mode

DDL 示例:

```
CREATE DATABASE %database%;

-- CREATE USER '%user%'@'%host%' IDENTIFIED BY '%password%';
GRANT ALL PRIVILEGES ON %database%.* TO '%user'@'%host%' IDENTIFIED BY '%password%' WITH GRANT OPTION;
FLUSH PRIVILEGES; 
```

Enter fullscreen mode Exit fullscreen mode

DML 示例:

```
>  select 'Hello, world.' as greetings;
+---------------+
| greetings     |
+---------------+
| Hello, world. |
+---------------+
1 row in set (0.00 sec) 
```

Enter fullscreen mode Exit fullscreen mode

<center>✿ ✿ ✿</center>

非常感谢您的阅读。如果这篇文章在某种程度上对某人有所帮助，我会很高兴: )