# 今天我学到了:为什么没有很多工具来压缩一个 csv 文件

> 原文：<https://dev.to/therealkevinard/today-i-learned-why-there-aren-t-many-tools-for-sql-ing-a-csv-37b>

我有很多头衔，我经常使用 CSV，因为它是非常常见的外行数据交换格式。我对 SQL 略知一二，我发现自己曾无数次地想知道为什么没有很多工具可以对电子表格运行 SQL。

我今天做了一点系统管理，发现自己又在想同样的事情了。不过这一次...我花了一分钟来思考这个问题。

现在我知道了: **Sqlite**

我发现的是怎么死的。臭气熏天。很简单，将 csv 文件直接转换成 sqlite 数据库。从那里，我可以去镇上。

1.  找到一个 csv，如果需要的话整理一下。(注意:你所有的列都需要一个标题——这对数据库来说是有意义的，对吗？)

2.  启动 sqlite: `sqlite3 some_db_file.db`

3.  将 csv 导入干净的数据库

```
 .mode csv
   .import my_csv_file.csv some_db_name 
```

Enter fullscreen mode Exit fullscreen mode

**搞定！** Sqlite 自动将你的电子表格移入一个新的数据库。针对它运行 SQL，构建一个使用它的 CLI 应用程序，等等——玩得开心！