# SQL:复制表和大规模更新

> 原文：<https://dev.to/theoklitosbam7/sql-copy-tables-and-massive-update-31ke>

## 路线图

在开发过程中，我们经常会犯错误，导致全部或部分数据丢失。更糟糕的是，当我们由于产品版本中的错误而丢失它们时。

在这篇文章中，我们将看到如何创建一个数据库表作为现有数据库表的备份，复制数据，并对这两个表进行大规模更新。我们将引用来自不同 RDBMSs 的相应 SQL 语句:MariaDB、PostgreSQL、MS SQL Server、IBM DB2、SQLite。

## MariaDB

### 创建表格

```
CREATE TABLE tbl2 LIKE tbl1; 
```

Enter fullscreen mode Exit fullscreen mode

`tbl2`表格将继承来自`tbl1`的所有属性。

### 复制数据

```
INSERT INTO tbl2
SELECT * FROM tbl1; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用`WHERE`子句只插入 tbl1 的一部分数据。

### 海量更新

```
UPDATE tbl1 a
INNER JOIN tbl2 b ON
a.key = b.key
SET a.column1 = b.column1; 
```

Enter fullscreen mode Exit fullscreen mode

## PostgreSQL

### 创建表格

```
CREATE TABLE tbl2 ( LIKE tbl1 INCLUDING ALL ); 
```

Enter fullscreen mode Exit fullscreen mode

### 复制数据

```
INSERT INTO tbl2
SELECT * FROM tbl1; 
```

Enter fullscreen mode Exit fullscreen mode

### 海量更新

```
UPDATE tbl1 a
SET a.column1 = b.column1
FROM tbl2 b
WHERE
a.key = b.key; 
```

Enter fullscreen mode Exit fullscreen mode

## MS SQL Server

### 创建表格并复制所有数据

```
SELECT * INTO tbl2
FROM tbl1 
```

Enter fullscreen mode Exit fullscreen mode

### 只创建表格

```
SELECT * INTO tbl2
FROM tbl1
WHERE 1 = 0; 
```

Enter fullscreen mode Exit fullscreen mode

### 海量更新

```
UPDATE a
SET a.column1 = b.column1
FROM tbl1 a
INNER JOIN tbl2 b ON
a.key = b.key; 
```

Enter fullscreen mode Exit fullscreen mode

## IBM DB2

### 创建表格

```
CREATE TABLE tbl2 LIKE tbl1; 
```

Enter fullscreen mode Exit fullscreen mode

### 复制数据

```
INSERT INTO tbl2
SELECT * FROM tbl1; 
```

Enter fullscreen mode Exit fullscreen mode

### 海量更新

```
UPDATE tbl1 a
SET a.column1 = b.column1
FROM tbl2 b
WHERE
a.key = b.key; 
```

Enter fullscreen mode Exit fullscreen mode

## SQLite

### 只创建表格

```
CREATE TABLE tbl2 AS
SELECT * FROM tbl1
WHERE 1 = 0; 
```

Enter fullscreen mode Exit fullscreen mode

### 仅复制数据

```
INSERT INTO tbl2
SELECT * FROM tbl1; 
```

Enter fullscreen mode Exit fullscreen mode

### 同时创建表格和数据

```
CREATE TABLE tbl2 AS
SELECT * FROM tbl1; 
```

Enter fullscreen mode Exit fullscreen mode

### 海量更新

```
UPDATE tbl1
SET
column1 = (SELECT b.column1
    FROM tbl2 b
    WHERE b.key = tbl1.key)
WHERE tbl1.key IN (SELECT b.key FROM tbl2 b); 
```

Enter fullscreen mode Exit fullscreen mode

在 SQLite 中，我们可以利用[行值方法](https://www.sqlite.org/rowvalue.html)，如果我们想要使用多个键:

```
UPDATE tbl1
SET
column1 = (SELECT b.column1
    FROM tbl2 b
    WHERE (b.key1, b.key2) = (tbl1.key1, tbl1.key2) )
WHERE (tbl1.key1, tbl1.key2) IN (
  SELECT b.key1, b.key2 FROM tbl2 b); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*最初发布于[https://www . coding notebook . eu/SQL-copy-tables-and-massive-update/](https://www.codingnotebook.eu/sql-copy-tables-and-massive-update/)*