# 错误:SQL 存储过程在实体框架中返回-1

> 原文：<https://dev.to/rachelsoderberg/bug-sql-stored-procedure-with-complex-types-returning-1-in-entity-framework-1gn7>

偶尔我喜欢写一些我最近遇到的 bug，以及我是如何着手解决它们的。这不仅有望帮助其他人更快地修复相同的 bug，还能巩固我头脑中的解决方案，并在我再次遇到它时给我一个参考点。所以不多说了，让我们来看看这个 bug 吧！

[![Ladybug](img/3f428b6ff8a603ea93bfb00a653f54de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t_YRuOd5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.mnn.com/asseimg/2017/05/lady-bug-on-leaf.jpg.653x0_q80_crop-smart.jpg)

一旦连接上，Visual Studio 的实体框架通常会使添加新存储过程的过程像向模型添加新表一样简单。不幸的是，当存储过程返回一个[复杂类型](https://docs.microsoft.com/en-us/ef/ef6/modeling/designer/data-types/complex-types)时，比如来自动态查询或临时表的一组数据，Entity Framework 将只返回关于其结果集中的列的元数据，而不执行任何逻辑。这是由于实体框架在执行这些特殊存储过程之前使用的 *[SET FMTONLY ON](https://docs.microsoft.com/en-us/sql/t-sql/statements/set-fmtonly-transact-sql?view=sql-server-2017)* SQL 命令，以保护数据免受潜在的危险更改。

幸运的是，一旦您意识到这种数据保存机制，从用户“gotmilk13531”这里的中找到的修复其实非常简单。用*改变存储过程设置 FMTONLY OFF*紧跟在 ALTER PROCEDURE AS 或 BEGIN 语句之后，如下所示:

```
ALTER     PROCEDURE [dbo].[se_GetSystemBOM_JF]
    @systemid int
AS
SET NOCOUNT ON
SET FMTONLY OFF;

IF EXISTS(
    SELECT * FROM tempdb.dbo.sysobjects o 
```

或者

```
ALTER PROCEDURE dbo.SearchProducts
  @SearchTerm VARCHAR(max)
AS
BEGIN
    SET FMTONLY OFF;
    DECLARE @query VARCHAR(max)
    SET @query = 'SELECT * FROM dbo.Products WHERE Name LIKE ''%' + @SearchTerm + '%'''
    EXEC(@query)
END 
```

一旦修改了存储过程，加载数据库 edmx 并打开 EF 模型浏览器。您必须从复杂类型、函数导入和存储过程/函数中删除您的存储过程，然后保存，我通常在此时重新加载 EF。接下来，使用典型的从数据库更新模型的方法将存储过程添加到 EF 中，然后保存并关闭 EF。最后，一定要返回并再次修改您的存储过程，以移除*SET FMTONLY OFF；*。

* * *

我希望这种快速解决方法能帮助其他人摆脱和我类似的困境，比我解决的时间要短一点！感谢阅读，下周见。