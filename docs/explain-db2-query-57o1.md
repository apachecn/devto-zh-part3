# 解释 DB2 查询

> 原文：<https://dev.to/adzubla/explain-db2-query-57o1>

使用所有表中的模式名称将 SQL 查询写入文件`query.sql`。

```
db2expln -database DATABASE -t -g -terminator ';' -f query.sql 
```

`db2advis`可以为查询建议一些索引:

```
db2advis -d DATABASE -i query.sql 
```