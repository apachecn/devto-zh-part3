# 使用 PostgreSQL 的超快速全文搜索

> 原文：<https://dev.to/ypkuby/super-fast-full-text-search-with-postgresql-58pi>

这是最初发布在我的博客上的，我在这里发布所有关于技术和系统管理员之类的东西！。

如果我告诉你，搜索数百万条记录并不复杂，只需几分钟就能完成，你会怎么想？大多数组织跳到了像 Elastic Search 这样的专用搜索引擎，但是他们大大低估了 PostgreSQL 的能力。

假设我们有一个包含 110 万条记录的表，我们想对它应用一个文本搜索向量。我们关心作者和出版商的名字。为了创建向量，我们需要取出旧数据，并将其放入一个包含向量的新列中。

是时候打破它了！我们的作者和出版商的名字只是字符串，例如作者是“杰克·斯派洛”，出版商是“一家电影公司”——我们希望能够开始键入“jac”或“mov cor”，并让它拾取杰克·斯派洛和/或电影公司。有道理！我们面临的问题是，我们不希望有两列，我们需要一个索引，以便我们可以快速搜索，所以我们在数据库表中创建一个新列，让我们把这个列简称为“tsv”。

首先，添加列，然后对其应用 GIN 索引。

```
ALTER TABLE searchable ADD COLUMN tsv tsvector;

CREATE INDEX searchable_gin_idx ON searchable USING GIN(tsv); 
```

现在，让我们相应地更新我们的表记录，您可以使用此 SQL 创建一个英语 TSVector，并立即将其应用于 TSV 字段。

`UPDATE searchable SET tsv = ( to_tsvector('english', author) || to_tsvector('english', publisher ) );`

这将开始使用我们串联的作者和出版商构建一个简单的 gin 索引。我们现在将能够执行简单和复杂的快速搜索查询。这是相同的数据，不影响速度。例如，如果我们想找到所有包含作者“jac parr”的条目，我们可以做:

`SELECT * FROM searchable WHERE tsv @@ to_tsquery('jac:* parr:*');`

很快，大量的结果呈现在我们面前。有趣的是，即使我们在查询中附加了通配符，PostgreSQL 仍然能够快速搜索并获得结果集。我们可以在 ORDER BY 子句中进一步应用 ts_rank_cd，根据权重对它们进行排序。

速度和一致性很重要。
如果我们对其运行 explain analyze，我们可以看到该查询的计划时间为 0.013 毫秒，执行时间为 0.067 毫秒。

如果我们在没有 GIN 索引的情况下运行相同的查询，我们看到计划花费了 0.121 毫秒，执行花费了 132.14 毫秒。

如您所见，这是一次重大的性能提升，我们获得了与之前相同的结果，但这次速度要快得多。因此，您可能不需要搜索产品的另一个原因是——如果您使用适当的索引优化了查询和数据库表，您就可以充分利用 PostgreSQL 的强大功能。