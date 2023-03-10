# r 语言:连接 MySQL 数据库

> 原文：<https://dev.to/maheshkay/r-language--connect-to-mysql-database-51ji>

当您在 r .中处理数据时，您可能会发现大多数在线网站和仪表盘都使用 MySQL 数据库。默认情况下，我注意到 mysql 似乎没有任何官方驱动程序。然而，你有足够的库允许你连接 MySQL。

没几个是——rmy SQL，RODBC，RJDBC。

在这个例子中，我们将利用 RMySQL 向您展示如何连接 MySQL 数据库。可以查看视频演示- [R 语言如何连接 MySQL 数据库](https://www.youtube.com/watch?v=k9EG2GzdfJk)。

[https://www.youtube.com/embed/k9EG2GzdfJk](https://www.youtube.com/embed/k9EG2GzdfJk)

让我们从第一步开始。

安装库。

```
install.packages("RMySQL") 
```

然后我们可以连接到数据库。填写各自的详细信息，如用户名、密码等。

```
library(RMySQL)

mydb = dbConnect(MySQL(), 
user='user', password='password', 
dbname='database_name', host='host') 
```

从这一点开始，您必须为您的特定 SQL 数据库操作运行查询。

您可以查看[参考手册](https://cran.r-project.org/web/packages/RMySQL/RMySQL.pdf)中的具体查询。

如果您使用的是 RStudio，还可以根据您处理数据的方式来查看表格数据输出。

尽管许多 R 开发人员使用 RMysql。我建议你也去看看其他的图书馆。然后决定哪一个适合您的工作流程。

我希望这里的信息能够帮助您使用 R lang 使用 mysql 和 mariadb 数据库。