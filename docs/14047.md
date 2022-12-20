# 什么是最适合存储大量文章和新闻的数据存储库？

> 原文：<https://dev.to/devfanooos/what-are-the-most-suitable-datastores-for-storing-a-huge-number-of-articles-and-news-b1>

我在目前的公司被分配到一个新的大项目。这个项目将从不同的来源收集大量的新闻文章。

整个要求仍然不清楚，但我们可以期待它的一部分。举个例子，

构建一些仪表板来显示收集到的新闻的统计数据。
全文搜索(精确、模糊和同义词)
为其他团队(尤其是数据分析团队)提供一种查询数据的方式。

对于这样一个项目，您建议使用什么样的数据存储库？

我相信这种类型的项目没有一个通用的解决方案。

首先，我考虑使用 *Elassandra* ，因为它结合了 *Cassandra* 和 *Elasticsearch* ，可以满足前两点(聚合和分析的 *Cassandra* 和全文搜索的 *Elasticsearch* )。

还是第三点不满足。数据分析人员更熟悉的是 *SQL* ，它不会 100%由 *Cassandra* 或 *Elasticsearch* 提供。

我考虑的另一种方法是为分析团队提供另一个存储，负责写入数据的应用程序会将数据写入两个存储。

你怎么想呢?