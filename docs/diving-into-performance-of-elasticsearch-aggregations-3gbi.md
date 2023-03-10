# 深入研究弹性搜索聚合的性能

> 原文：<https://dev.to/raoulmeyer/diving-into-performance-of-elasticsearch-aggregations-3gbi>

Elasticsearch 在提高性能方面做了很多，但我们从未注意到，我们能做些什么来进一步提高它呢？这是我在研究我们正在使用的一些大型聚合的性能时问自己的问题。在这篇文章中，我对 Elasticsearch 中的缓存做了一个基本的解释，然后通过两个实验来验证缓存和查询是如何交互的。

## elastic search 如何缓存？

Elasticsearch 有不同级别的缓存，它们一起工作以确保它尽可能快地响应。所有的缓存级别都有相同的承诺:*接近实时的*响应。这意味着您得到的响应既快又与当前索引中的数据匹配(或几乎匹配)。

### 请求缓存

Elasticsearch 有自己的智能请求缓存。它将根据底层索引的更新来更新这个缓存，从而确保缓存总是准确的。当然也有一些陷阱，例如:

> 即使在索引设置中启用了请求缓存，大小大于 0 的请求也不会被缓存。

请求缓存可能不起作用的其他原因是当您的响应包含一个在每次请求时都会改变的值时。例如，如果您的响应包含当前日期或一些随机生成的数字，这将使响应不可缓存。

如果您想了解更多关于如何调整请求缓存的信息，请看一下文档。

### 查询缓存

在更深的层次上，过滤器类型查询的结果可以被缓存到称为位集的二进制表示中。就像请求缓存一样，每当索引中的相关内容更新时，这个缓存就会自动更新。Elasticsearch 仅缓存适用于大量文档的查询:

> 只有包含超过 10，000 个文档(或总文档的 3%，以较大者为准)的段才会缓存该位集。

之所以这样做，是因为对于较小的段，评估查询可能会更快。在没有缓存的情况下，Elasticsearch 已经优化了多少性能，通过添加缓存很容易使事情变得更慢。你可以在这里找到更多关于查询缓存的信息。

### 字段数据缓存

字段数据缓存与聚合非常相关。正如文档中所说:

> 它将所有字段值加载到内存中，以便提供对这些值的基于文档的快速访问。

没有为字段数据缓存预留足够的内存将会降低聚合速度。您可以监控字段数据缓存的使用情况，并根据需要进行调整。点击[此处](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-fielddata.htm)了解更多信息。

## 对提取常用查询元素有帮助吗？

查询缓存似乎对许多真实世界的聚合非常有益。在索引的过滤子集上执行一些聚合是非常常见的。在这种情况下，Elasticsearch 可以重用过滤吗？或者我们能帮助它做到这一点吗？

让我们比较以下查询的性能。第一个查询分别为两个聚合指定了相同的筛选器:

```
{  "size":  0,  "aggregations":  {  "1":  {  "filter":  {  "match":  {  "search_field":  "text"  }  },  "aggregations":  {  "items":  {  "top_hits":  {  "size":  100,  "_source":  {  "includes":  "field1"  }  }  }  }  },  "2":  {  "filter":  {  "match":  {  "search_field":  "text"  }  },  "aggregations":  {  "items":  {  "top_hits":  {  "size":  100,  "_source":  {  "includes":  "field2"  }  }  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

第二个查询将这个过滤器提取到一个更高的级别，这将使聚合共享结果。我们需要将过滤器包装在一个 *bool.filter* 中，以确保得分是相同的:

```
{  "query":  {  "bool":  {  "filter":  [  {  "match":  {  "search_field":  "text"  }  }  ]  }  },  "size":  0,  "aggregations":  {  "1":  {  "top_hits":  {  "size":  100,  "_source":  {  "includes":  "field1"  }  }  },  "2":  {  "top_hits":  {  "size":  100,  "_source":  {  "includes":  "field2"  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

对于这个测试，我们禁用了请求缓存，但是查询缓存和字段数据缓存仍然可以工作。我们已经确保过滤查询的段实际上大于 10，000 个文档。这意味着查询缓存应该为此发挥作用，并且这两个查询之间的查询时间应该没有差别。这正是我们所看到的:

[![Aggregation nesting graph](img/7a0b428c7665701d99eb5f62bfd8d8f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nCD0qWPb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/KmW6pTH.png)

这两种解决方案之间没有性能差异。看起来查询缓存做得很好。记住对查询缓存的要求，您可能仍然更喜欢第二种变化。在这两种情况下，现场数据缓存也同样工作良好。

## 聚合并行运行吗？

在日常工作中，我看到很多这样的情况，我们在一个查询中放置了大量的聚合。这让我想知道，聚合实际上是并行运行的吗？或者，我们是否可以通过对每个聚合执行一次查询来缩短响应时间？

在这个测试中，我们运行与之前相同的查询。我们在一个查询中测试了 1、2、5 和 10 个聚合。我们将其与拆分聚合时进行比较，因此每个聚合都有自己的查询。

[![Aggregation parallelization](img/d8604cc0d25b0d57512f15e3ba4a8311.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ic2EyfgH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/gvgXbtg.png)

当我们为每个聚合提供自己的查询并进行 *msearch* 时，我们看到了显著的性能提升。在 10 个聚合时，加速接近 2 倍。在这个测试中，Elasticsearch 实例运行在一个具有 2 个可用 CPU 的 docker 容器中，因此这个加速比是您所能期望的最好的。

显然，默认情况下，聚合不只是并行运行。因此，如果希望缩短响应时间，将聚合拆分成多个查询可能是有意义的。这仅适用于 CPU 不是瓶颈的情况，因为通过拆分查询，您将总共使用更多的 CPU 时间。

## 结论

那么，提取常见的查询元素有帮助吗？一般来说，你应该。你不需要*去做，因为 Elasticsearch 可以针对这些情况进行优化。如果您的过滤器不适合查询缓存，将通用查询元素移到聚合中更高的位置可能仍会稍微提高性能。*

聚合是并行运行的吗？他们默认不会。使用 *msearch* 将它们分开可能是明智的，只要你还没有受到 CPU 的限制。在尚未充分利用的集群上，这可以显著缩短响应时间。