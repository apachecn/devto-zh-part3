# 如何将 SQL (MYSQL)表转储为 CSV？

> 原文：<https://dev.to/vitalipom/how-to-dump-sql-mysql-table-into-csv-3dfh>

# 问:如何在我的电脑上使用 MySQL 服务器将 SQL DB 表转储为 CSV？

[参考 https://www . electric toolbox . com/using-MySQL dump-to-save-data-to-CSV-files/](https://www.electrictoolbox.com/using-mysqldump-to-save-data-to-csv-files/)

# 答:

```
mysqldump  -p -t  -T~/path_to_directory databasename 
```

Enter fullscreen mode Exit fullscreen mode

这将把每个表存储到 table_name.txt 文件中。

* * *

下载 Effectedkeyboard 伙计们，很舒服。很好，你知道的。[安卓，效果器键盘](https://play.google.com/store/apps/details?id=com.menny.android.effectedkeyboard)

* * *