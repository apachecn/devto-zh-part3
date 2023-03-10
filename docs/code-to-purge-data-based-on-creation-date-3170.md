# 根据创建日期清除数据的代码

> 原文：<https://dev.to/am2/code-to-purge-data-based-on-creation-date-3170>

<figure>[![](img/7a068d4f10431ba4e56855900927a935.png)](https://www.youtube.com/watch?v=nHlJODYBLKs) 

<figcaption id="caption-attachment-1743">只有一件事比清理旧数据更能激起喜悦。该死的。</figcaption>

</figure>

昨天，[我谈到了批量删除](https://dev.to/2019/04/purging-data-with-batched-deletes/)，前天我谈到了[我是多么喜欢删除数据](https://dev.to/2019/04/the-thrill-of-purging-old-data/)，以及为什么有必要这样做，尽管我是一个数据打包员。

今天，让我们只看一个场景，以及我喜欢如何处理它。根据我的经验，这是最常见的清除需求之一(可能也是最简单的)，所以首先，让我们看看业务需求。

## 吹扫要求

在这种情况下，我们将在数据创建后将其保留 X 天。然后我们删除它。就是这样。x 可能是 3 天，也可能是 3 年——没关系，我们将遵循相同的设计模式。

在当今世界，我们会产生大量的日志数据、传感器数据、遥测数据等。所有这些数据对 T2 来说都是有价值的。但只是暂时的。最终，所有这些粒度数据变得不那么有用，不值得保留。也许它被聚合，总结，或者也许它只是被扔掉。

您将会有许多具有更复杂需求的数据，但是我想您也会惊讶于有多少数据基于其创建而具有简单的基于日期的保留。

## 表格设计

当我为这种类型的业务需求设计表格时，我总是喜欢使用相同的模式，除非由于某种原因这种设计模式不起作用:

1) **使用标识列。**如果是日志数据，并没有真正的[自然键](https://en.wikipedia.org/wiki/Natural_key)，所以使用代理键是有意义的。即使看起来确实有一个自然键，在这里它也不是一个很好的选择。数据生命周期是数据身份的重要组成部分(参见我在那里做了什么？)，并且数据只保留 X 天的事实是跳过自然键并使用标识列形式的代理键的好理由。您应该将 ID 列作为主键&聚集索引。

2) **包含数据插入的日期&时间。**我们将保留创建后 X 天的数据…所以…我们*有点*需要知道它是什么时候创建的。这似乎是显而易见的，但是我见过很多表在创建时没有考虑清理保留，只有当表很大时才考虑保留需求。您应该在创建日期创建非聚集索引。

这两列都将不断增加。这意味着，当数据被插入到表的“末尾”时，我们可以从表的“开头”删除数据——我们将有两个索引来帮助我们确定删除什么和保留什么。为什么我不直接使用日期时间列，而跳过标识列呢？日期/时间是一个糟糕的主键。有时候两行同时被插入，然后你的唯一性就见鬼去了。ID 列解决了这个问题。

如果你正在寻找一个比我更聪明的人使用设计模式的真实例子，看看 Ola Hallengren 的 [SQL Server 维护解决方案](https://ola.hallengren.com)中的`dbo.CommandLog`。

```
CREATE TABLE [dbo].[CommandLog](
    [ID] [int] IDENTITY(1,1) NOT NULL,
    [DatabaseName] [sysname] NULL,
    [SchemaName] [sysname] NULL,
    [ObjectName] [sysname] NULL,
    [ObjectType] [char](2) NULL,
    [IndexName] [sysname] NULL,
    [IndexType] [tinyint] NULL,
    [StatisticsName] [sysname] NULL,
    [PartitionNumber] [int] NULL,
    [ExtendedInfo] [xml] NULL,
    [Command] [nvarchar](max) NOT NULL,
    [CommandType] [nvarchar](60) NOT NULL,
    [StartTime] [datetime] NOT NULL,
    [EndTime] [datetime] NULL,
    [ErrorNumber] [int] NULL,
    [ErrorMessage] [nvarchar](max) NULL,
    CONSTRAINT [PK_CommandLog] PRIMARY KEY CLUSTERED ([ID] ASC)
        WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)
); 
```

您会注意到用于代理键的`ID`列，以及用于数据创建时间的`StartTime`列。Ola 没有包含关于`StartTime`的非聚集索引，但是我添加了一个来帮助我清理。Ola 的代码确保 StartTime 被可靠地填充，但是根据您的情况，您可能希望有一个默认的约束或触发器来确保它被可靠地填充。

## 清理时间

让我们看看我们想要用来做清理的算法。为了演示起见，我们用 Ola 的`CommandLog`表。假设我们需要在日志中保留数据 60 天。让我们还假设我们已经做了一些测试，并确定我们想要批量删除 5000 行。就像我昨天讨论的那样，我们希望在批处理之间休息一下，以防止破坏事务日志，所以我们将在每次删除之间等待半秒钟。

步骤 1)找到我们想要删除的 ID 值的范围。我们将使用日期/时间列来查找 id 的范围。

```
--StartID is where we start, and MaxID is the maximum ID that we want to delete
SELECT @StartID = MIN(ID), @MaxID = MAX(ID) 
FROM dbo.CommandLog
WHERE StartTime < DATEADD(DAY,-60,GETDATE()); 
```

第 2 步)首先，让我们设置循环来执行批处理。这是最难的部分，所以我喜欢在弄清楚实际的 work/delete 语句是什么之前先理清逻辑。

```
--We'll start at the start, and increment up until we hit that MaxID
WHILE (@StartID < @MaxID)
    BEGIN 
        --increment @StartID by our batch size
        SELECT @StartID = @StartID + 5000;
        --Delete stuff
        --Wait for half a second before looping again
        WAITFOR DELAY '00:00:00.5'
    END; 
```

第 3 步)好，现在我们需要写一个`DELETE`语句。那个`--Delete stuff`评论看起来很棒，但是功能性不是很强。

```
DELETE TOP (5000) x 
FROM dbo.CommandLog AS x 
WHERE x.ID < @StartID AND x.ID < @MaxID; 
```

现在我们只需要把它们缝合在一起……

```
--StartID is where we start, and MaxID is the maximum ID that we want to delete
SELECT @StartID = MIN(ID), @MaxID = MAX(ID) 
FROM dbo.CommandLog
WHERE StartTime < DATEADD(DAY,-60,GETDATE());

--We'll start at the start, and increment up until we hit that MaxID
WHILE (@StartID < @MaxID)
    BEGIN 
        --increment @StartID by our batch size
        SELECT @StartID = @StartID + 5000;
        --Delete stuff
        DELETE TOP (5000) x 
        FROM dbo.CommandLog AS x 
        WHERE x.ID < @StartID AND x.ID < @MaxID;
        --Wait for half a second before looping again
        WAITFOR DELAY '00:00:00.5'
    END; 
```

现在你只需要安排它。将 SQL 代理作业设置为每周运行一次并删除数据。

## 我有一个简单的按钮

我发现自己编写了复制这种模式的代码，一遍又一遍地清除数据。最终，我实现了自动化。在 GitHub 上我的 [DBA 数据库中，我创建了一个存储过程来处理这个场景的删除。](https://github.com/amtwo/dba-database)

我只需要执行我的`Cleanup_TableByID`过程:
，而不是编写特定于表的代码

```
EXEC dbo.Cleanup_TableByID
    @DbName = 'DBA',
    @SchemaName = 'dbo',
    @TableName 'CommandLog',
    @DateColumnName 'StartTime',
    @IDColumnName 'ID',
    @RetainDays int = 60,
    @ChunkSize int = 5000,
    @LoopWaitTime time = '00:00:00.5',
    @Debug bit = 0; 
```

看看我的 [Cleanup_TableByID 程序](https://github.com/amtwo/dba-database/blob/master/stored-procedures/dbo.Cleanup_TableByID.sql)，安装我的 [DBA 数据库](https://github.com/amtwo/dba-database)。安装很容易(指导在 readme 中)，并且是免费的(它发布在 GPL 下)。

* * *

#### 补充阅读

*   [批量删除清除数据](https://dev.to/2019/04/purging-data-with-batched-deletes/)
*   [清除旧数据的快感](https://dev.to/2019/04/the-thrill-of-purging-old-data/)
*   [DBA 数据库 Github Repo](https://github.com/amtwo/dba-database)

基于创建日期清除数据的 post [代码最早出现在](https://am2.co/2019/04/code-to-purge-data-on-creation-date/) [Andy M Mallon - AM](https://am2.co) 上。