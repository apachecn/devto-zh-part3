# r 语言:连接 CouchDB 数据库

> 原文：<https://dev.to/maheshkay/r-language--connect-to-couchdb-database--3nag>

我从未真正有时间从 NoSQL 数据库中获取大量数据。但是我最近被要求找到与他们交流的方法。所以我试图找到连接 CouchDB 和 MongoDB 的方法。似乎很多人都在使用 CouchDB 数据库。因此，我认为那些希望从 R 语言中与 CouchDB 进行交互的人可以看看这篇文章，从中寻找一条出路。

**第一步。安装驱动程序**

你可以看看像[沙发](https://github.com/ropensci/sofa)和 [R4CouchDB](https://github.com/wactbprot/R4CouchDB/) 这样的司机。

(还有许多其他驱动程序，但其中两个足以满足大多数需求)。你可以在这里查看视频说明。 [R 语言连接 CouchDB 数据库](https://www.youtube.com/watch?v=rFnGA4NZXq8)。

[https://www.youtube.com/embed/rFnGA4NZXq8](https://www.youtube.com/embed/rFnGA4NZXq8)

假设你想要 R4CoouchDB。所以还是装吧。

```
install.packages("R4CouchDB") 
```

**第二步。创建连接对象**

```
conn <- cdbIni() 
```

第三步。检查服务器响应。

```
conn$serverName 
```

**第三步。探索具体的查询**

从这里开始，您可以继续进行查询以浏览数据。在这里，您可以存储或更新和删除数据中的内容。

你可以在 [github 自述文件](https://github.com/wactbprot/R4CouchDB)中查看具体的查询。

你也可以在 [Wiki](https://cwiki.apache.org/confluence/display/COUCHDB/R) 上查看一些例子。

其他参考资料:

1.  还可以学习[如何将 R 语言连接到 MySQL 数据库](https://dev.to/maheshkay/r-language--connect-to-mysql-database-51ji)。
2.  了解如何从 R 语言连接到 SQLite[。](https://dev.to/maheshkay/r-language--connect-to-sqlite-database-1adf)