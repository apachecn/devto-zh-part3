# MySQL/MariaDB:类似于 Petya 勒索软件，用于 MySQL 和' root'@'% '访问

> 原文：<https://dev.to/setevoy/mysql-mariadb-like-petya-ransomware-for-mysql-and-root-access-11hk>

[![](img/dbc564519c9ee236cfc98fdd2330c214.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--t-ekjqil--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2014/08/mariadb_logo.png) *这个故事发生在 2017 年 10 月 6 日，现在用英文添加这个帖子。[的原始帖子](https://rtfm.co.ua/mysqlmariadb-like-petya-ransomware-dlya-baz-dannyx-i-root/) (Rus)几乎是在众所周知的[而不是 Petya](https://en.wikipedia.org/wiki/2017_cyberattacks_on_Ukraine) 袭击事件之后写的——这就是为什么它被用在标题中。*

我有一个新项目分配给我。当我开始调查它的现有设置时，我震惊了。

所以。

一个数据科学项目，一堆 MariaDB 服务器，每个服务器有 10 到 150 个数据库。

在检查服务器和它们的数据库的时候，顺便启用了`general log`，我突然发现有一台服务器除了一个奇怪的`PLEASE_READ`名字:
之外根本没有数据库

```
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| PLEASE_READ        |
| information_schema |
| mysql              |
+--------------------+ 
```

这个数据库中有一个表有着更奇怪的名字——`WARNING`:

```
MariaDB [(none)]> use PLEASE_READ;
Database changed
MariaDB [PLEASE_READ]> show tables;
+-----------------------+
| Tables_in_PLEASE_READ |
+-----------------------+
| WARNING               |
+-----------------------+ 
```

嗯…也许以前的管理员/开发人员/开发人员对这个服务器/数据库有一些警告？

阅读其内容:

```
MariaDB [PLEASE_READ]> select * from WARNING;
+----+-----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------+-------------------------+
| id | warning                                                                                                                                             | Bitcoin_Address                    | Email                   |
+----+-----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------+-------------------------+
|  1 | Send 0.5 BTC to this address and go to this site http://es7ocnlet5vyulh5.onion/ to recover your database! SQL dump will be available after payment! | 14UhC8s4hUUCmwT31RqVbDB7dgoBxT4oXg | backupbase@mail2tor.com |
+----+-----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------+-------------------------+ 
```

[![](img/d7a1f764fe91bd6f09829950fb11afb7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6NoPFBvx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2017/10/what_facepalm.png)

邮箱上“*[【backupservice@mail2tor.com】](mailto:backupservice@mail2tor.com)*”我谷歌了下一个帖子:

[https://www . guardicore . com/2017/02/0-2-BTC-strikes-back-now-attaming-MySQL-databases/](https://www.guardicore.com/2017/02/0-2-btc-strikes-back-now-attacking-mysql-databases/)

但那只是整个历史的开始！

在接下来的调查中发现:

1.  **MySQL `root`没有密码**
2.  `root`可从任何地方访问，即`'root'@'%'`
3.  每个带有 MariaDB 服务器的 AWS EC2 实例都有外部 IP
4.  所有这些实例使用的单个 AWS 安全组有两个允许来自 *0.0.0.0 网络…* 的端口 22 和 3306 的规则

```
MariaDB [mysql]> select host,password from user where user = 'root';
+-----------------------------+-----------+
| host                        | password  |
+-----------------------------+-----------+
| localhost                   |           |
| hostname.domain.com         |           |
| 127.0.0.1                   |           |
| %                           |           |
+-----------------------------+-----------+ 
```

从互联网上的任何地方访问:

```
$ mysql -u root -h 52.***.***.37
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 55560
...
MariaDB [(none)]> 
```

再一次:

[![](img/3dd9aa362851acb1c4b7116dfc4e0ade.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G798RUWJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2017/10/picard-facepalm.jpg)

结果，11 个数据库服务器中的 4 个服务器上的所有数据库都被清除了。

在其他服务器上——root 只能从本地主机访问——这保存了他们的数据库。

在其中一台服务器上，我在禁用所有这些实例的网络访问之前看到了连接尝试:

```
...
43293 Connect   Access denied for user 'root'@'182.255.63.166' (using password: NO) 
```

中国，虽然这个什么都不给:

```
irt:            IRT-CYHADCL-CN
address:        UNIT 04,7/F,BRIGHT WAY TOWER,NO. 33 MONG KOK ROAD,KOWLOON, hong kong hong kong
e-mail:         admin@kwaihingidc.com
abuse-mailbox:  admin@kwaihingidc.com
admin-c:        KHNT2-AP
tech-c:         KHNT2-AP
auth:           # Filtered
mnt-by:         MAINT-CYHADCL-CN 
```

*“今天天气真好”* (c)

### 类似的帖子

*   T012/16/2013 t1tomcat:使用 JDBCRealm 和 MySQL 或 Oracle T3 进行用户身份验证
*   <small>02/21/2019</small>[OpenVPN:OpenVPN 接入服务器设置和 AWS VPC 对等配置](https://dev.to/setevoy/openvpn-openvpn-access-server-set-up-and-aws-vpc-peering-configuration-5fpg) <small>(0)</small>