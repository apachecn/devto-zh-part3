# r 语言:连接 SQLite 数据库

> 原文：<https://dev.to/maheshkay/r-language--connect-to-sqlite-database-1adf>

当你为 r .开发脚本时，你可能会遇到必须访问不同类型的数据库的情况。

SQLite 是文件级数据库，没有多少人将其用于大数据存储或特定于分析的存储。此外，在某些情况下，您可能会访问这个数据库。

例如，现在基于 Android 的应用和服务越来越多地使用 Sqlite。您可能需要清理和读取这些数据。

所以在这里我想告诉你如何使用 R 编程语言连接到 SQLite 数据库。

可以查看 [R 语言连接 SQLite 数据库](https://www.youtube.com/watch?v=ILO1Y8CSie8)教程获取视频说明。

[https://www.youtube.com/embed/ILO1Y8CSie8](https://www.youtube.com/embed/ILO1Y8CSie8)

下面是您可以查看的代码示例。我假设您正在使用 RStudio 执行下面的代码。

```
library(RSQLite)

setwd("/sqlitedatafolder")
sqlite <- dbDriver("SQLite")
conn <- dbConnect(sqlite,"rlang_db.sqlite3") 
```

这个例子基本上是把你和数据库连接起来。从这里开始，您可以运行进一步的查询，并根据您的要求处理数据和可视化。