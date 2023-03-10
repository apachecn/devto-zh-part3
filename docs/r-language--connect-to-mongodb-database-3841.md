# r 语言:连接到 MongoDB 数据库

> 原文：<https://dev.to/maheshkay/r-language--connect-to-mongodb-database-3841>

让我们学习如何将 MongoDB 数据库与 r 连接起来。如今，许多人在处理大量非关系数据时，会使用基于文档的 noSQL 数据库。回到从 R 语言连接数据库的主题。

我们要做的第一件事是检查一些 R lang 可以用来连接 mongodb 的驱动程序。我们将在下面的例子中使用的驱动程序是 Rmongo。您可以从 github 页面查看 R mongo 文档。

你可以在这里查看视频说明。 [R 语言:连接 MongoDB 数据库](https://www.youtube.com/watch?v=JBEKJflNV2g)。

[https://www.youtube.com/embed/JBEKJflNV2g](https://www.youtube.com/embed/JBEKJflNV2g)

**第一步。安装驱动程序**

在蒙古人和蒙古人之间。我选择 RMongo。如果您希望使用特定的驱动程序进行连接，您可能需要阅读 mongolite 的 github 页面。

假设你想要 RMongo。所以还是装吧。

```
install.packages("RMongo") 
```

这应该可以解决安装部分。

**第二步。创建连接**

为了建立连接，我们必须首先使用软件包。然后建立联系。因此，让我们利用以下代码行来实现相同的目的。

```
library(RMongo)
rmng <- mongoDbConnect('db') 
```

或者你也可以用。

```
rmng <-  mongoDbConnect('db', 'localhost', 27017) 
```

这应该有助于连接 mongodb 数据库中的数据库“db”。下一件事是打印出连接对象的响应。

**第三步。检查服务器响应。**

要验证连接是否已建立并且似乎没有任何错误，您可能需要尝试以下代码。

```
print(dbShowCollections(rmng)) 
```

这个输出应该向您显示连接响应。

**第四步。探索具体的查询**

从这里去哪里？现在，从这里开始，您必须针对存储在文档中的数据库项进行查询。从现在开始，您所做的一切都是针对您的数据库的。并且可能需要额外的操作代码。

您可能想从 r document for[r mongo](https://github.com/tc/RMongo)中了解更多关于查询的信息。

其他参考资料:

1.  还可以学习[如何将 R 语言连接到 MySQL 数据库](https://dev.to/maheshkay/r-language--connect-to-mysql-database-51ji)。
2.  学习[如何从 R 语言](https://dev.to/maheshkay/r-language--connect-to-sqlite-database-1adf)连接 SQLite。
3.  了解如何从 R 语言连接到 CouchDB