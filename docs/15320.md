# Postgres 中的事件存储，多租户

> 原文：<https://dev.to/kspeakman/event-storage-in-postgres-multi-tenant-25hc>

我之前写过 Postgres 中的[事件存储。当时我没有解决的一个问题是多租户场景。多个租户增加了大量数据的可能性。特别是，索引最终会变得非常大，以至于开始影响性能。(这就是希望吧？🤑).我们在之前的产品中处理这一问题的方式是将租户的数据隔离在它自己的模式中。然而，当我们想要跨租户处理事件时，这会导致问题。例如，当多个租户属于同一父组织时，提供汇总报告。或者测量系统上的活动。当通过模式分离租户时，生成一个跨所有租户获取事件的查询是一件痛苦的事情。](https://dev.to/kspeakman/event-storage-in-postgres-4dk2)

## 分区

拥有逻辑上分离的租户数据，但又拥有单个事件表的便利的一种方法是使用 Postgres 的表分区。他们在最近的版本中增加了很多这个特性。而在 Postgres 11 中，这种特性尤为强烈。这是一个按租户 ID 划分的事件表版本。看起来都差不多！

### 主表

```
CREATE TABLE IF NOT EXISTS Event
(
    SequenceNum bigserial NOT NULL,
    TenantId uuid NOT NULL,
    StreamId uuid NOT NULL,
    Version int NOT NULL,
    Type text NOT NULL,
    Meta jsonb NOT NULL,
    Data jsonb,
    LogDate timestamptz NOT NULL DEFAULT now(),
    CONSTRAINT pk_event_sequencenum PRIMARY KEY (TenantId, SequenceNum),
    CONSTRAINT uk_event_streamid_version UNIQUE (TenantId, StreamId, Version)
) PARTITION BY LIST (TenantId); 
```

*主表上的约束需要包含分区键，这里是`TenantId`。*

### 租户分区

不幸的是，Postgres 还没有自动创建分区的功能。因此，在为租户插入数据之前，首先必须为它创建一个分区。通常，在添加新租户时会有一个配置过程，因此创建分区只是该过程的一部分。下面是一个创建 TenantId = 847 ed 1889 E8 B4 d 238 EB 49126 EBD 77 a4d 的租户的例子。

```
CREATE TABLE IF NOT EXISTS Event_847ED1889E8B4D238EB49126EBD77A4D
PARTITION OF Event FOR VALUES IN ('847ED1889E8B4D238EB49126EBD77A4D')
; 
```

在幕后，每个分区都有自己的索引。所以你不会得到一个单一的巨大指数。当您跨租户查询数据时，Postgres 可以在分区上并行运行查询，然后聚合它们。

### 插入事件

您可以像将数据插入到单个表中一样插入数据，Postgres 会自动将数据路由到适当的分区。

```
INSERT
  INTO Event
     ( TenantId
     , StreamId
     , Version
     , Type
     , Meta
     , Data
     )
VALUES
     ( '847ED1889E8B4D238EB49126EBD77A4D'
     , 'A88F94DB6E7A439E9861485F63CC8A13'
     , 1
     , 'EmptyEvent'
     , '{}'
     , NULL
     )
; 
```

### 按顺序查询

为了支持按照事件发生的顺序读取事件，您可以运行如下查询。

```
SELECT *
  FROM Event
 WHERE SequenceNum > 0
 ORDER
    BY SequenceNum
 LIMIT 1000
; 
```

该查询支持读取最多 1000 个批次。我避免使用`OFFSET`,因为一旦偏移值变大，效率就会降低。每个侦听器通常都会跟踪它处理的最后一个 SequenceNum。

我可以在 WHERE 子句中添加另一个条件，比如`AND SequenceNum <= 1000`，而不是使用`LIMIT`。但是由于并发性，可能会跳过序列号(见下文)。虽然这是个小点。

### 按流查询

按流查询和以前一样，只是我们现在还需要提供 TenantId。

```
SELECT *
  FROM Event
 WHERE TenantId = '847ED1889E8B4D238EB49126EBD77A4D'
   AND StreamId = 'A88F94DB6E7A439E9861485F63CC8A13'
 ORDER
    BY Version
; 
```

## 其他好东西

### 事件通知

每当事件被添加到事件表的任何分区时，您都可以触发 Postgres 通知。这是我目前使用的。

```
DROP TRIGGER IF EXISTS trg_EventRecorded ON Event;
DROP FUNCTION IF EXISTS NotifyEvent();

CREATE FUNCTION NotifyEvent() RETURNS trigger AS $$

    DECLARE
        payload text;

    BEGIN
        -- { sequencenum }/{ tenant id }/{ stream id }/{ version }/{ event type }
        SELECT CONCAT_WS( '/'
                        , NEW.SequenceNum
                        , REPLACE(CAST(NEW.TenantId AS text), '-', '')
                        , REPLACE(CAST(NEW.StreamId AS text), '-', '')
                        , NEW.Version
                        , NEW.Type
                        )
          INTO payload
        ;

        PERFORM pg_notify('eventrecorded', payload);

        RETURN NULL;

    END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_EventRecorded
    AFTER INSERT ON Event
    FOR EACH ROW
    EXECUTE PROCEDURE NotifyEvent()
; 
```

当插入一个事件时，它将触发对通道`eventrecorded`的通知。这是一个有效载荷的例子。

```
# sequence num
#  / tenant id
#                                   / stream id
#                                                                    / version
#                                                                      / type
  2/847ed1889e8b4d238eb49126ebd77a4d/a88f94db6e7a439e9861485f63cc8a13/2/EmptyEvent 
```

这种有效负载格式很大程度上受到 MQTT 主题路径的启发。

事件本身可能太大而不适合通知负载。因此，我提供了足够的事件数据，让侦听器知道他们是否愿意费力加载它。通常侦听器只关心事件的类型和序列号。然后，它们通常从它们处理的最后一个序列号开始加载成批的事件。所以中间的比特可能是 YAGNI 违反。但是如果侦听器想要加载特定的事件而不是批处理，那么包含它是正确的。

为了监听事件通知，SQL 部分很简单:

```
LISTEN eventrecorded; 
```

但是代码部分会根据您的语言而有所不同。就个人而言，我需要与 Npgsql 库一起使用的编码模式感觉有点痛苦。我认为这与通过库的抽象泄露的数据库细节有更大的关系。我以一个非常必要的生产者/消费者队列结束。在添加了所有的错误处理之后，它在 F#中看起来不是很好。但是也可以。

### 并发事件

我们添加到事件表中的唯一约束称为`uk_event_streamid_version`，它将通过乐观并发为我们捕捉并发冲突。

例如，假设用户试图在流上执行操作。逻辑加载流，它有 4 个事件。最后一个事件的版本是 4。我们运行我们的代码并决定，基于这个流的当前状态，我们应该生成 2 个新事件。由于上一个版本是 4，这些事件应该分别具有版本= 5 和版本= 6。然后我们将这些事件插入到事件表中。

同时，另一个用户试图对同一个流执行不同的操作。这个请求正在另一个线程(或另一台计算机)上处理，并且不知道第一个用户的请求。它读取相同的 4 个事件，并决定生成 1 个版本= 5 的新事件。但是，另一个用户的操作就在我们的操作之前提交了他们的 DB 事务。因此，当我们的代码试图保存版本= 5 的事件时，它将失败。Postgres 将触发唯一的索引冲突。

只要我们在保存之前适当地计算每个新事件的预期版本，唯一约束将防止并发冲突。

#### 序列缺口

当出现并发冲突时，它会在 SequenceNum 中产生一个间隙。这就是 Postgres 中自动递增序列的工作方式。即使事务被中止，序列仍会递增。否则，管理序列的回滚会使插入变得复杂并降低性能。不要纠结于序列间隙——这只是为了排序。

> ⚠️ **兼职作家**
> 
> 正如下面评论中指出的(感谢亚历山大·兰格！)，这个实现适合一个“单身写手”。但是由于 Postgres 序列的工作方式，多个并发的作者可能会引入一个微妙的错误:事件有时可能会以不同于 SequenceNum 的顺序提交。由于侦听器依赖于按顺序加载事件，这可能会导致侦听器错过事件。
> 
> 参见下面的中的[了解支持并发写入器的实现。因此，它还会生成无间隙序列号。这可能会对插入性能产生影响，但我还没有测量。](#comment-node-148394)

### 数据管理

租户数据现在也很容易单独删除或备份，因为它实际上位于自己单独的表中。例如，假设 TenantId = 847 ed 1889 E8 B4 d 238 EB 49126 EBD 77 a4d 请求我们删除他们的数据，那么删除这些数据是相当容易的。

```
-- DANGER DANGER
DROP TABLE Event_847ED1889E8B4D238EB49126EBD77A4D CASCADE; 
```

假设你有某种滚动备份，最终备份也会被遗忘。

# 结论

随着数据集变大，这种按租户划分事件流的方法有助于提高性能。在某些时候，我们可能需要更进一步，将我们的数据划分到不同的数据库节点上。这种基本的表结构似乎也适合使用 Citus Data(最近被微软收购)之类的东西进行基于节点的分区。但是，当一个总体指标成为性能瓶颈时，上述方法应该能够解决第一个棘手的扩展障碍。它并不比一张桌子更费力！

/∞