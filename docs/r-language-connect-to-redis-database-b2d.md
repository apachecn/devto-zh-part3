# r 语言:连接到 Redis 数据库

> 原文：<https://dev.to/maheshkay/r-language-connect-to-redis-database-b2d>

让我们学习如何将 Redis 数据库与 r 连接起来。有一些数据库，如 *Redis* 通常用于特定的数据集，例如游戏服务器、排名、温度监视器等。r 几乎没有开放源码和高级包，允许您连接 Redis 数据库。

所以在这里，我看一下 R 中的 Redux 包，它允许您连接 redis 数据库。还有一些其他 Redis 特定的包也有助于连接。

你可以在这里查看视频说明。 [R 语言:连接 Redis 数据库](https://www.youtube.com/watch?v=RqPiiXGxOAo)。

**第一步。安装驱动程序**

与 redit 数据库的连接有多个驱动程序。然而，让我们坚持积极发展的一个。这里 [redux 驱动](https://cran.r-project.org/web/packages/redux/)对于连接 redis 数据库很有用。

您可以通过遵循 R shell 中的代码来安装这个包。

```
install.packages("redux") 
```

之后你就可以安装 redux 库了。

**第二步。创建连接**

现在您已经安装了库，您可以创建 hireredis 对象，建立与服务器的连接。

```
r <- redux::hiredis() 
```

在这里，您会注意到，默认情况下，它将连接到(127.0.0.1)上的 redis 和端口 6379。

您可以执行`redis$PING()`

现在，您可以使用 redis_api 来连接多个 redis 命令。

**第三步。探索具体的查询**

现在您已经学习了如何连接 redis 和 R，并设法执行了一些基本命令。从这里开始，您必须进一步探索更多的查询。

您可能想从 Rdocumentation 中了解更多关于 [Redux](https://github.com/richfitz/redux) 的查询。

**附加参考:**

1.  还可以学习[如何将 R 语言连接到 MySQL 数据库](https://dev.to/maheshkay/r-language--connect-to-mysql-database-51ji)。
2.  了解如何从 R 语言连接到 SQLite[。](https://dev.to/maheshkay/r-language--connect-to-sqlite-database-1adf)
3.  了解如何从 R 语言连接到 CouchDB
4.  了解如何[将 R 语言连接到 MongoDB 数据库](https://dev.to/maheshkay/r-language--connect-to-mongodb-database-3841)。