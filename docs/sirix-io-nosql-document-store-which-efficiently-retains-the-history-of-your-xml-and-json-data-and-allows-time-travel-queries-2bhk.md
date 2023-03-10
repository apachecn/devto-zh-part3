# sirix . io——NoSQL 文档库，可以有效地保留 XML 和 JSON 数据的历史，并允许时间旅行查询

> 原文：<https://dev.to/johanneslichtenberger/sirix-io-nosql-document-store-which-efficiently-retains-the-history-of-your-xml-and-json-data-and-allows-time-travel-queries-2bhk>

几周前我已经发了帖子，但我真的很想得到评论，我想我必须提供一些背景信息。

任何类型的问题、建议和帮助都将受到极大的感谢，因为这是我的一个开源项目(6 年前我在康斯坦茨大学学习期间也是为其他人服务的)。从那以后，我花了无数个小时来提出版本化存储系统的想法，特别适合时变数据的分析任务。

例如，您可以通过 XQuery 处理器查询本地存储的 JSON 数据的历史:

```
let  $statuses  :=  jn:open('mycol.jn','mydoc.jn',  xs:dateTime('2019-04-13T16:24:27Z'))=>statuses  let  $foundStatus  :=  for  $status  in  bit:array-values($statuses)  let  $dateTimeCreated  :=  xs:dateTime($status=>created_at)  where  $dateTimeCreated  >  xs:dateTime("2018-02-01T00:00:00")  and  not(exists(jn:previous($status)))  order  by  $dateTimeCreated  return  $status  return  {"revision":  sdb:revision($foundStatus),  $foundStatus{text}} 
```

该查询基于时间戳在特定修订版中打开数据库/资源(因为数据库当时是新的),然后搜索创建于 2018 年 2 月 1 日之后的 Twitter 帖子，并且仅搜索先前修订版中不存在的状态。然后，它输出修订号并投影找到的状态文本。

特别是，我很乐意讨论您需要什么文档，接下来哪些步骤是必要的(基于成本的查询优化器、复制/分区...)，API 添加或更改...

我想在几天内发布 0.9.0 版本，但也许你有一些明显缺失的东西，我喜欢任何类型的评论:-)

[https://sirix.io](https://sirix.io)

一些功能包括:

*   当前的原生 XML 和 JSON 存储(其他数据类型可能紧随其后)，
    事务性的、版本化的、类型化的用户定义的索引结构，一旦事务提交，就会自动更新。

*   通过 XPath-axis 扩展，我们不仅支持空间导航，还支持时间导航(未来::，过去::，第一个::，最后一个::…)。此外，由于我们的整体版本化方法，我们提供了几个时态 XQuery 函数。JSON 资源的时态导航是通过内置的 XQuery 函数完成的。

*   内存中的路径摘要，在事务提交期间持久保存，并始终保持最新。

*   数据库级别的可配置版本控制(完整、增量、差异和一种新的滑动快照算法，该算法平衡读取和写入，而不会引入写入峰值，写入峰值通常在中间完整转储期间生成，通常会被写入)。

*   由于事务性写入时复制(COW)语义，日志结构的顺序写入和随机读取。这提供了很好的好处，例如并发读取事务没有锁定，它充分利用了闪存磁盘的优势，同时避免了它们的弱点。

*   完全隔离当前每个资源的 N 个读事务和一个写事务。

*   页面结构深受 ZFS 的启发，因此也形成了一棵树。我们将实现一个类似的 merkle-tree，并将每个页面的散列存储在父指针中，用于完整性检查。

*   对 XQuery 和 XQuery Update 的支持源于 brackit(.org)。

*   另外还支持 XML 层的移动。

*   如果合适，自动将后代轴的路径重写到子轴。

*   导入两个 XML 文档之间的差异，即在 XML 文档的第一个版本被导入之后，算法试图用最少的操作来更新 Sirix 资源，以将第一个版本改变为新版本。

*   一种快速的基于 ID 的 diff 算法，能够确定存储在 Sirix 中的资源的任何两个版本之间的差异，可选地考虑节点的散列。

*   节点的子节点的数量、后代的数量、散列以及 ORDPATH / DeweyID 标签可选地与每个节点一起存储，该标签在磁盘上被压缩以有效地确定文档顺序以及支持分层节点标签的其他良好属性。目前，如果启用了散列，则总是存储子代的数量和后代的数量。

*   灵活的后端。

*   磁盘上每个页面的可选加密和/或压缩。