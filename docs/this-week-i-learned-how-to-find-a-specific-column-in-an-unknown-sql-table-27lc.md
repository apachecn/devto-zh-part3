# 在未知的 SQL 表中查找特定的列

> 原文：<https://dev.to/rachelsoderberg/this-week-i-learned-how-to-find-a-specific-column-in-an-unknown-sql-table-27lc>

我的公司使用 Aptean Intuitive ERP 作为我们销售处理系统的一部分，在 Intuitive 和我们的处理应用程序之间的“中间人”是 SQL 数据库。我们自行开发的处理器应用程序从入站销售文件中获取数据，将数据插入相应的 SQL 列，并创建 PDF 项目和销售订单文件，然后直观地处理这些 PDF 文件并将数据插入系统。

我本周的任务之一是为这一系列事件添加折扣，因为在当前状态下，经理必须手动更新每个订单的折扣表和直观信息。

为了完成这项任务，我首先确定在直观中管理折扣的位置以及它们对应的列名，这样我就可以确保在处理传入的销售文件时更新正确的表值。在确定了列名之后，我遇到了一个以前从未遇到过的问题，“如何在不手动搜索所有 100 多个表的情况下确定哪个表包含特定的列？”

经过一番挖掘，我发现了一个可以在相当简单的 SQL 查询中完成的解决方案:
*SELECT
c . name AS ' column name '
，t . name AS ' TableName '
FROM
sys . columns c
JOIN
sys . tables t ON c . object _ id = t . object _ id
其中
c . name LIKE ' % SOD _ disc percent % '
ORDER BY
TableName，ColumnName*

将这个查询细分一下:
sys.columns 为表或视图的每一列返回一行
sys.tables 为每个表返回一行

根据对象 ID 连接 sys.columns 和 sys.tables，并根据列名进行过滤，这样我们就可以使用已知的列来确定它属于哪个表。使用 SELECT all 会显示更多关于表和列的信息，但是在这个例子中，我只关心获取表名。

使用该查询，我能够确定在创建项目和销售订单 pdf 之前，需要在处理器应用程序中更新哪些表，以便在达到直观效果时更新适当的字段。