# 在选择图形数据库时，如何评估模式的存在与否？

> 原文：<https://dev.to/just_a_coder_jp/how-do-you-assess-the-presence-or-absence-of-schemas-when-choosing-a-graph-database-oge>

我认为图形数据库的优点之一是避免了繁重的连接操作。这意味着从一个节点到其他连接节点的遍历将更加紧凑。一些图形数据库具有无模式特性，如 Neo4j。在很多情况下，存储不属于任何模式的节点真的方便吗？为了获得具有图形结构的数据的高速遍历(使用属性值),我认为图形数据库的设计基本上必须将模式约束到节点和边。如果不是这样的话，我猜想数据库可能不会强调速度。

```
// Cyper
MATCH (person:Person)-[knows:Knows]->(Person)
WHERE knows.since > 2015
  // ↑ The DB must one by one check see if the property 'since' exists and be comparable with '2015' before evaluating op '>'.
RETURN id(knows) 
```

另一方面，我部分同意无模式规范的便利性。例如，它适用于历史数据和波动数据。因为模式迁移有时会破坏与旧记录兼容性。我尝试在数据序列中使用无模式图数据库。我同意的另一个例子是原型。然而，享受这些便利只是从产品开发开始到寿命结束的总时间的一小部分。对于决定选择使用图形数据库而不是通常的 RDB 来说，这似乎有点太昂贵了。

高速遍历多条边不是使用图形数据库的最大目的之一吗？