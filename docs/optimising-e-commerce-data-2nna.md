# 优化电子商务数据-了解您的数据(KYD)

> 原文：<https://dev.to/sumitkumar1209/optimising-e-commerce-data-2nna>

### 优化电子商务数据—了解您的数据(KYD)

[![](img/1090c538c43d1f72bc0abce31f2b6be1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bYZiMEkj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2AtsdMwVAFslnq5XEHfAqThQ.gif)T3】来源 [WebFusion](http://www.thewebfusion.com/services/web-development/website-web-design-and-graphic-designing/) 

在我以前的组织(选择。AI)，我们的客户(阅读电子商务商店)从每月处理最多 100–1000 个独立访客的小型客户，到每月处理大约 30，000-50，000 个独立流量的中型客户，再到最终在网站上拥有超过 50，000 个定期购物者的大型客户。根据他们的流量大小，每个商店都有不同的组织定价计划，所以我们对他们的投资也应该根据他们的投资回报率进行规划。

为了向所有类型的客户提供更快和同等的价值，我们构建了独立于商店目录和流量大小的解决方案。随着越来越多的商店成为我们的客户，这种方法开始让我们花费更多，因为他们在计算资源和能力方面需要几乎相同的投资。

[![](img/96facf8980e8c76187ba8e261bc09f35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OKzD5lDG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/804/1%2AEe_ftZOzIdkgggs-2j4YqA.gif) 

<figcaption>来源 [BK 网站设计](https://bkwebdesigns.com/web-development/)</figcaption>

在这一系列博客中，我将谈论我们通过“了解”和理解我们的数据所包含的内容而解决的主要问题，以及他们的解决方案帮助我们根据商店的目录和流量大小优化资源分配，而不损害质量、相关性和组织提供的功能。

#### 存储序列化的超对数(HLL)寄存器

我们的主要问题是在基于文档的数据存储中处理聚合的用户事件分析(在我们的例子中是 MongoDB)。我们的主要目标是统计不同维度和细分市场的独立访客数量，维度可以是活动或实验，细分市场可以是设备或流量来源。

为了获得不同时间段内的独立访问者数量(仅在查询时可用)，我们需要将 HLL 注册中心的序列化数据存储在数据存储中，该数据存储将被解序列化和合并以给出所需的值。

考虑到单个字符为 8 位，字符串总长度为 8192 个字符，这个字符串的字节长度为 8192。它与值无关，它表示值可以小到 1，也可以大到 50k，所有的字符串长度都一样。

#### 产品目录的展开键

我们的客户来自电子商务的不同领域，并有不同的和独特的产品提供给最终消费者。由于我们以相似的方式对待每个客户数据的主要原则，我们面临的最常见的问题是一个客户的结构不良数据的问题对另一个客户的结构清晰的分层数据的处理的干扰。

我们允许客户导入任意多的产品属性，他们将获得对这些属性的过滤和聚合支持。这种方法导致了键爆炸，这意味着在基于高度索引的文档的存储系统(在我们的例子中是 Elasticsearch)中要索引更多的字段，并最终占用大量的磁盘空间和更多的时间来索引和检索过滤器上的数据。

***系列中的下一个帖子***

*   [优化基于文档的存储-了解您的数据(KYD)](https://medium.com/real-spark/optimising-document-based-storage-know-your-data-kyd-214e884af5b9)
*   [优化高索引文档存储-了解您的数据(KYD)](https://medium.com/real-spark/optimising-highly-indexed-document-storage-know-your-data-kyd-c5c11deaa736)

* * *