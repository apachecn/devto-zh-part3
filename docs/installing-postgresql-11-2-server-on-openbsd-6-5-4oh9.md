# OpenBSD 6.5 上的 PostgreSQL 11.2:安装

> 原文：<https://dev.to/nabbisen/installing-postgresql-11-2-server-on-openbsd-6-5-4oh9>

这篇文章展示了如何在 [OpenBSD](https://www.openbsd.org/) 上安装 [PostgreSQL](https://www.postgresql.org/) 并设置它。
我去年写过同样的主题，现在 OpenBSD 中的 PostgreSQL 版本已经升级到 10.3 - > 11.2，因为 OpenBSD 已经有了 6.3 - > 6.5。令人高兴的是，我所要做的只是遵循同样的流程。

<center>✿ ✿ ✿</center>

## 环境

*   操作系统:OpenBSD 6.5
*   数据库:PostgreSQL 11.2

## *图例

在代码区域，前导的`#`意味着由超级用户执行；相当于使用`doas`命令(作为 root)，而前面的`$`是指一般用户。

## 教程

### 安装包

```
#  pkg_add postgresql-server 
```

Enter fullscreen mode Exit fullscreen mode

### 初始化数据库

将用户切换到在上面的包安装中创建的`_postgresql`:

```
#  # in order to avoid an error about permission:
#  cd /var/postgresql/
  #  su _postgresql 
```

Enter fullscreen mode Exit fullscreen mode

然后运行 [`init_db`](https://www.postgresql.org/docs/11/app-initdb.html) 创建一个数据库集群:

```
$  initdb -D /var/postgresql/data/ -U postgres --auth=md5 --pwprompt --encoding=UTF-8 --locale=xx_XX.UTF-8 
```

Enter fullscreen mode Exit fullscreen mode

取决于你的环境。
对我来说，是`ja_JP.UTF-8`。

为了不指定语言环境，运行时不使用`--encoding=UTF-8 --locale=xx_XX.UTF-8`，而是使用

```
- --encoding=UTF-8 --locale=xx_XX.UTF-8 + --no-locale 
```

Enter fullscreen mode Exit fullscreen mode

况且`--auth=md5`和`--pwprompt`都是为了安全起见。

将打印以下内容:

```
The files belonging to this database system will be owned by user "_postgresql".
This user must also own the server process.

The database cluster will be initialized with locale "ja_JP.UTF-8".
initdb: could not find suitable text search configuration for locale "ja_JP.UTF-8"
The default text search configuration will be set to "simple".

Data page checksums are disabled. 
```

Enter fullscreen mode Exit fullscreen mode

您将被问到:

```
Enter new superuser password: 
Enter it again: 
```

Enter fullscreen mode Exit fullscreen mode

这是根用户的密码，又名`postgres`。

然后打印:

```
creating directory /var/postgresql/data ... ok
creating subdirectories ... ok
selecting default max_connections ... 30
selecting default shared_buffers ... 128MB
selecting dynamic shared memory implementation ... posix
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

Success. You can now start the database server using:

    pg_ctl -D /var/postgresql/data/ -l logfile start 
```

Enter fullscreen mode Exit fullscreen mode

好的。退出`_postgersql`用户:

```
$  exit 
```

Enter fullscreen mode Exit fullscreen mode

### 启动 PostgreSQL 服务器

激活守护进程并启动它:

```
#  rcctl enable postgresql
#  rcctl start postgresql
postgresql(ok) 
```

Enter fullscreen mode Exit fullscreen mode

已完成: )

### 安装后

[`psql`](https://www.postgresql.org/docs/11/static/app-psql.html) 用作 PostgreSQL 的基于终端的前端。
以 root 用户身份运行，您将被要求输入上面拒绝的密码:

```
$  psql -U postgres 
```

Enter fullscreen mode Exit fullscreen mode

下面是 DDL 的例子。
创建[数据库](https://www.postgresql.org/docs/11/sql-createdatabase.html)和[角色](https://www.postgresql.org/docs/11/sql-createrole.html) :

```
CREATE DATABASE %DATABASE%;
CREATE ROLE %USER% WITH ENCRYPTED PASSWORD '%PASSWORD%';
GRANT ALL PRIVILEGES ON DATABASE %DATABASE% TO %USER%; 
```

Enter fullscreen mode Exit fullscreen mode

[`CREATE USER`](https://www.postgresql.org/docs/11/sql-createuser.html) 可用，而不是`CREATE ROLE`，只是别名。

另一种配置角色有效期和数据库编码的方式:

```
CREATE ROLE %USER% LOGIN ENCRYPTED PASSWORD '%PASSWORD%' NOINHERIT VALID UNTIL 'infinity';
CREATE DATABASE %DATABASE% WITH ENCODING='UTF8' OWNER=%USER%; 
```

Enter fullscreen mode Exit fullscreen mode

退出:

```
\q 
```

Enter fullscreen mode Exit fullscreen mode

<center>✿ ✿ ✿</center>

stoorigin 快乐: )