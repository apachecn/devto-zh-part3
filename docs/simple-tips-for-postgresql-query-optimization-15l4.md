# PostgreSQL 查询优化的简单技巧

> 原文：<https://dev.to/paveltiunov/simple-tips-for-postgresql-query-optimization-15l4>

一个简单的查询优化技巧可以将数据库性能提高 100 倍。有一次，我们建议一个拥有 10TB 数据库的客户使用基于日期的多列索引。结果，他们的日期范围查询速度提高了 112 倍。在这篇文章中，我分享了五个简单但仍然强大的 PostgreSQL 查询优化技巧。

在 [Cube.js](https://github.com/statsbotco/cube.js) 部署期间，我经常使用这些技术来优化我们客户的 PostgreSQL 数据库，该数据库包含数十亿个数据点。

为了简单起见，我在测试数据集上运行了本文的示例。虽然它没有显示实际的性能改进，但是您将会看到我们的技巧解决了一系列重要的优化问题，并且在真实的案例场景中运行良好。

## 解释分析

Postgres 对众所周知的`EXPLAIN`命令有一个很酷的扩展，叫做 [`EXPLAIN ANALYZE`](https://www.postgresql.org/docs/9.3/static/using-explain.html#USING-EXPLAIN-ANALYZE) 。不同之处在于，`EXPLAIN`根据收集的数据库统计数据显示查询成本，而`EXPLAIN ANALYZE`实际运行它来显示每个阶段的处理时间。

我强烈推荐您使用`EXPLAIN ANALYZE`,因为在很多情况下,`EXPLAIN`显示出较高的查询成本，而执行时间实际上较少，反之亦然。最重要的是，`EXPLAIN`命令将帮助你理解一个特定的索引是否被使用以及如何使用。

查看索引的能力是学习 PostgreSQL 查询优化的第一步。

## 每个查询一个索引

索引是表的物化副本。它们只包含表中的特定列，因此您可以根据这些列中的值快速查找数据。Postgres 中的索引还存储用于加速原始表扫描的行标识符或行地址。

它总是在存储空间和查询时间之间进行权衡，大量的索引会给 DML 操作带来开销。然而，当读取查询性能是一个优先考虑的问题时，比如业务分析，这通常是一个很好的方法。

我建议为每个唯一查询创建一个索引，以获得更好的性能。在这篇文章中进一步了解如何为特定的查询创建索引。

## 在索引中使用多个列

让我们回顾一下以下不带索引的简单查询的解释分析计划:

```
EXPLAIN ANALYZE SELECT line_items.product_id, SUM(line_items.price)
FROM line_items
WHERE product_id > 80
GROUP BY 1 
```

解释分析器返回:

```
HashAggregate (cost=13.81..14.52 rows=71 width=12) (actual time=0.137..0.141 rows=20 loops=1)
Group Key: product_id
-> Seq Scan on line_items (cost=0.00..13.25 rows=112 width=8) (actual time=0.017..0.082 rows=112 loops=1)
Filter: (product_id > 80)
Rows Removed by Filter: 388
Planning time: 0.082 ms
Execution time: 0.187 ms 
```

该查询扫描所有行项目，以查找 id 大于 80 的产品，然后对按该产品 id 分组的所有值求和。

现在我们将索引添加到这个表:

```
CREATE INDEX items_product_id ON line_items(product_id) 
```

我们创建了一个 [B 树索引](https://en.wikipedia.org/wiki/B-tree)，它只包含一列:`product_id`。在阅读了大量关于使用索引的好处的文章后，人们可以期待这种操作会提高查询效率。抱歉，坏消息。

由于我们需要对上面查询中的 price 列进行求和，我们仍然需要扫描原始表。根据表的统计信息，Postgres 将选择扫描原始表而不是索引。问题是，index 缺少一个`price`列。

我们可以通过添加一个价格列来调整这个索引，如下所示:

```
CREATE INDEX items_product_id_price ON line_items(product_id, price) 
```

如果我们重新运行解释计划，我们会看到我们的索引是第四行:

```
GroupAggregate (cost=0.27..7.50 rows=71 width=12) (actual time=0.034..0.090 rows=20 loops=1)
Group Key: product_id
-> Index Only Scan using items_product_id_price on line_items (cost=0.27..6.23 rows=112 width=8) (actual time=0.024..0.049 rows=112 loops=1)
Index Cond: (product_id > 80)
Heap Fetches: 0
Planning time: 0.271 ms
Execution time: 0.136 ms 
```

但是将 price 列放在首位会如何影响 PostgreSQL 查询优化呢？

## 多列索引中的列顺序

我们发现在前面的查询中使用了多列索引，因为我们包含了两列。有趣的是，在定义索引时，我们可以对这些列使用另一种顺序:

```
CREATE INDEX items_product_id_price_reversed ON line_items(price, product_id) 
```

如果我们重新运行 explain analyze，我们会看到没有使用`items_product_id_price_reversed`。这是因为该指数首先按`price`排序，然后按`product_id`排序。使用这个索引将导致一次全索引扫描，**，这几乎等同于扫描整个表**。这就是为什么 Postgres 选择对原始表使用扫描。

将列放在第一位是一个很好的做法，因为您在筛选器中使用的唯一值最多。

## 过滤器+联接

是时候弄清楚特定连接查询的最佳索引集了，该查询也有一些过滤条件。通常，你可以通过试错来达到最佳效果。

与简单过滤一样，选择最严格的过滤条件，并为其添加一个索引。

让我们考虑一个例子:

```
SELECT orders.product_id, SUM(line_items.price)
FROM line_items
LEFT JOIN orders ON line_items.order_id = orders.id
WHERE line_items.created_at BETWEEN '2018-01-01' and '2018-01-02'
GROUP BY 1 
```

这里我们在`order_id`上加入，在`created_at`上过滤。这样，我们可以创建一个多列索引，首先包含`created_at`，第二个包含`order_id`，第三个包含【T4:】

```
CREATE INDEX line_items_created_at_order_id_price ON line_items(created_at, order_id, price) 
```

我们将得到下面的解释计划:

```
GroupAggregate (cost=12.62..12.64 rows=1 width=12) (actual time=0.029..0.029 rows=1 loops=1)
Group Key: orders.product_id
-> Sort (cost=12.62..12.62 rows=1 width=8) (actual time=0.025..0.026 rows=1 loops=1)
Sort Key: orders.product_id
Sort Method: quicksort Memory: 25kB
-> Nested Loop Left Join (cost=0.56..12.61 rows=1 width=8) (actual time=0.015..0.017 rows=1 loops=1)
-> Index Only Scan using line_items_created_at_order_id_price on line_items (cost=0.27..4.29 rows=1 width=8) (actual time=0.009..0.010 rows=1 loops=1)
Index Cond: ((created_at >= '2018-01-01 00:00:00'::timestamp without time zone) AND (created_at <= '2018-01-02 00:00:00'::timestamp without time zone))
Heap Fetches: 0
-> Index Scan using orders_pkey on orders (cost=0.29..8.30 rows=1 width=8) (actual time=0.004..0.005 rows=1 loops=1)
Index Cond: (line_items.order_id = id)
Planning time: 0.303 ms
Execution time: 0.072 ms 
```

可以看到，`line_items_created_at_order_id_price`是用来减少按日期扫描的条件。之后，它会使用`orders_pkey`索引扫描加入订单。

日期列通常是多列索引中第一列的最佳候选列之一，因为它以可预测的方式降低了扫描吞吐量。

## 结论

以上 PostgreSQL 查询优化技巧将帮助您将多 GB 数据库的查询速度提高 10-100 倍。他们可以以 80/20 的方式解决您的大多数性能瓶颈。这并不意味着您不应该用`EXPLAIN`来仔细检查您的查询，以了解真实世界的情况。