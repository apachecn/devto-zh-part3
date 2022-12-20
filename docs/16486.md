# SQLite 3.25 增加了窗口功能，改进了 ALTER TABLE

> 原文：<https://dev.to/lefebvre/sqlite-325-adds-window-functions-and-improves-alter-table-4bc7>

SQLite 3.25 版本有两个显著的变化:窗口功能和一个改进的 ALTER TABLE 命令。

## 改变表格

如果您使用 SQLite 已经有一段时间了，那么您可能知道 ALTER TABLE 命令总是非常严格。它实际上只允许您更改表名或添加新列。您不能修改或删除列。这意味着，为了修改或删除表中的列，您通常使用一个多步骤的过程:按照您想要的方式创建新表，将数据复制到新表中，然后重命名这两个表，这样新表现在就具有了原始表的名称。这是一场争论。因为这是手动的，它会破坏任何使用旧名称的触发器或视图。

现在有了 SQLite 3.25，您可以直接重命名表上的列，这将根据需要更新触发器和视图。

语法如您所料:

```
ALTER TABLE MyTable RENAME COLUMN OldColumn TO NewColumn; 
```

在 Xojo 代码中，使用 SQLExecute 方法将该命令发送到数据库。下面是一个更改列名的示例:

```
Dim sql As String = "ALTER TABLE Team RENAME COLUMN Coach To HeadCoach;"
DB.SQLExecute(sql) 
```

遗憾的是，您仍然不能删除一个列，因此您必须求助于上面描述的手动方法。

## 窗口功能

根据 SQLite 文档:

> 窗口函数是一种特殊的 SQL 函数，其输入值取自 SELECT 语句结果集中一行或多行的“窗口”。

SQLite 现在有这些内置的窗口函数:

*   row_number()
*   排名()
*   密集等级()
*   百分比排名()
*   累计距离()
*   不完整的
*   图层(expr)、图层(expr、offset)、图层(expr、offset、default)
*   lead(expr)，lead(expr，offset)，lead(expr，offset，default)
*   第一个值(表达式)
*   最后一个值(表达式)
*   第 N 个值(表达式，N)

此示例使用 row_number 函数根据已排序的城市名称分配行号，同时仍按团队名称对整体结果进行排序:

```
SELECT Name, City, row_number() OVER (ORDER By City) AS row_num FROM Team ORDER BY Name; 
```

窗口函数功能强大，可以很快变得复杂。在[官方 SQLite 窗口函数文档页面](https://www.sqlite.org/windowfunctions.html)阅读更多关于它们的信息。