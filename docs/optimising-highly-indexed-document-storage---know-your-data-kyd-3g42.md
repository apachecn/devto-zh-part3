# 优化高索引文档存储-了解您的数据(KYD)

> 原文：<https://dev.to/sumitkumar1209/optimising-highly-indexed-document-storage---know-your-data-kyd-3g42>

### 优化高索引文档存储—了解您的数据(KYD)

本博客是[了解你的数据(KYD)](https://dev.to/sumitkumar1209/know-your-data-kyd-39a6) 系列和[优化电子商务数据](https://dev.to/sumitkumar1209/optimising-e-commerce-data-2nna)子系列的延续。在[以前的博客](https://dev.to/sumitkumar1209/optimising-document-based-storage---know-your-data-kyd-1hp8)中，我提到了使用压缩作为优化我们不太倾向于手动使用的字段存储的解决方案，在这里我解决了高度索引的文档存储中的键爆炸问题。在我的例子中，它是 ElasticSearch，在应用了多个数据建模更改后，我能够将索引大小减少 60 倍。

#### 背故事:

由于我们支持多种类型的电子商务客户，我们需要以商店显示的形式提供他们的用户数据，这意味着在我们的组织数据存储中存储和发送回许多不同的字段。

当不同的商店有不同类别的字段时，问题就出现了，我们必须接收所有信息，以提供正确的数据。

<figure>[![](img/ba363ff33475f507633239484dc112ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EvVKFeHi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/750/0%2AgRdNOOKWkniF_zGd) 

<figcaption>来源 [GIPHY](https://giphy.com/gifs/disneystudios-disney-9V5kjx3MRcVyE3kYpN)</figcaption>

</figure>

我们的主要原则是不采样任何数据，并使用与商店服务相同的过滤器，将功能放在最上面，这在存储大小上扼杀了我们，即使商店的目录很小。这里的问题在于被索引的字段和索引产生的存储成本。

<figure>[![](img/da55bbc4b6215eaa00a09e763365dfa5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZGhHvDT---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ARl2FNrnXDphJbeCB) 

<figcaption>来源 [GIPHY](https://giphy.com/gifs/disneystudios-disney-3Xw6TGuAa39J2X3a1q)</figcaption>

</figure>

#### 想法:

当我们考虑将 Elasticsearch 版本从 2.x 升级到 6.x 时，解决这个键爆炸问题的灵感和动力来了。Elasticsearch 开始通过将默认值设置为 1000 来限制映射中的字段数量。即使这个设置是可调的，保持在低水平也是更好的状态。因此，我开始查看索引，看在哪里可以减少需要索引的字段数量。解决办法是把田地分成两部分

1.  只需应用过滤的字段
2.  需要用于聚合的字段

这种分离给我们带来的好处是减少了需要索引以支持聚合的字段数量。

我们最初的文档看起来与此类似，其中出现了一些跨产品的公共字段，如`_id`、`price`以及一些可能根据产品类型而变化的字段，如如果是服装之类的东西，它们将有`size`和`color`作为属性，如果是家具或餐具之类的东西，它们将有`material`、`luster`等。

```
[
  {
    "\_id": "product id 1",
    "price": 232,
    "size": [
      "x",
      "xl"
    ],
    "color": [
      "red",
      "black"
    ],
    "views\_lastweek": 100,
    "views\_desktop\_lastweek": 80,
    ...
  },
  {
    "\_id": "product id 2",
    "price": 14,
    "material": [
      "steel",
      "brass"
    ],
    "luster": [
      "silver"
    ],
    "views\_lastweek": 40,
    "views\_desktop\_lastweek": 20,
    ...
  }
] 
```

我们将假设我们不需要在商店的任何地方将`luster`显示为选项，并且`material`必须显示在侧面导航/过滤小部件中。类似地，`size`不需要显示在侧边小部件中，而`color`必须填充在那里。

#### 目标:

我决定带着三个目标来解决这个问题:

1.  以减少需要索引的键的数量。
2.  为了不损害我们目前用旧模型提供的查询结果的质量，也为了不减少任何功能。
3.  保持可伸缩性，以处理一个商店可以拥有的任意数量的唯一字段

#### 流程:

**I .减少属性的数量**

为了实现上述目标，首要步骤是将字段分离成两种类型的过滤器关键字和聚集器关键字。

现在，任何只需要过滤而不需要聚合的关键字将帮助我们将其从索引中删除，并将其用作所有产品目录中通用字段的值，例如`tags`、`attributes`等。

因此，以上面的例子和假设为例，使用`size`和`luster`作为唯一的过滤关键字，我将现有的数据模型转换为

```
[
  {
    "\_id": "product id 1",
    "price": 232,
**"tags": [  
      "size->x",  
      "size->xl"  
    ],**  
    "color": [
      "red",
      "black"
    ],
    "views\_lastweek": 100,
    "views\_desktop\_lastweek": 80,
    …
  },
  {
    "\_id": "product id 2",
    "price": 14,
    "material": [
      "steel",
      "brass"
    ],
  **"tags": [  
      "luster->silver"  
    ],**  
    "views\_lastweek": 40,
    "views\_desktop\_lastweek": 20,
    …
  }
] 
```

您会注意到，我们能够将两个索引键`size`和`luster`减少到一个单独的`tags`字段。

**二。减少指标键的数量**

正如您在上面的示例中看到的，我们在每个产品上都有一些指标键，由于细分市场数量或持续时间的增加，这些指标键会增加到 100 个左右

例如，

指标类型=[浏览量、购买量、购物车添加量、…]，

持续时间=[上周、上月、昨天、2018 年 1 月等。】

细分=【桌面、移动、平板、广告、电子邮件等。]

为了减少这些关键因素，我根据商店在这些指标中的表现，评估了每个产品的数字分数，并将其分为高、中、低文本值。例如，如果商店收到 100 个添加到购物车中的产品，并且当前产品有 30 个添加到购物车中的高性能产品，那么它将被放置在高细分市场中，而不是仅收到 1 个添加到购物车中并被放置在低细分市场中的产品。

以此为原则，我将所有的度量键简化为 3 个键，每个片段一个

```
[
  {
    "\_id": "product id 1",
    "price": 232,
    "tags": [
      "size->x",
      "size->xl"
    ],
    "color": [
      "red",
      "black"
    ],
    **"metrics\_h": [  
      "views\_lastweek",  
      "views\_desktop\_lastweek"  
    ],**  
    "metrics\_l": [

    ],
    "metrics\_m": [

    ]
  },
  {
    "\_id": "product id 2",
    "price": 14,
    "material": [
      "steel",
      "brass"
    ],
    "tags": [
      "luster->silver"
    ],
  **"metrics\_m": [  
      "views\_lastweek",  
      "views\_desktop\_lastweek"  
    ],**  
    "metrics\_l": [

    ],
    "metrics\_h": [

    ]
  }
] 
```

这可以以单键`metrics`和值为`views_lastweek -> h`的形式存储，但我们的要求是对字段进行索引级别提升，如“*_h”应该提升 30，“*_m”应该提升 20，依此类推。

**三。不是索引字段，而是保存在数据存储中**

产品目录中的一些字段应该按原样交付，不需要进行查询、过滤、聚合或排序。这些字段在映射中被标记为`“index”: false`，这进一步帮助减少了索引的存储。

**IV:保持低索引配置文件**

我们倾向于不在我们的数据存储中进行任何部分匹配查询或匹配短语查询，这给了我们将大多数[索引选项声明为`docs`](https://www.elastic.co/guide/en/elasticsearch/reference/6.0/index-options.html) 以保持最低索引占用空间的优势。

```
{
  "index\_options": "docs",
  "type": "keyword"
} 
```

#### 基准:

现在用数字说话吧。

通过第一步的优化，我能够实现

1.  对于我们的一个客户，索引时间从 60 分钟减少到 2 分钟，相当于将商店产品目录索引到 Elasticsearch 的时间减少了 30 多倍，尽管两次都使用了批量索引。
2.  索引大小从 7168 MB (7 GB)减少到仅仅 220 MB。
3.  键的数量从+30k 减少到超过 250 个，我们仍然有大约 750 个键来满足弹性搜索的默认限制

在应用了优化的第二步之后，我能够实现

1.  之前优化为 2 分钟的步进过程，现在进一步缩短为 50 秒
2.  索引存储大小从 220 MB 减少到 110 MB
3.  钥匙数量从 250 个进一步减少到 200 个左右

现在，所有的优化都已就绪，在减少索引字段并将一些字段标记为非索引字段后，在 Elasticsearch 2.0 中，索引大小从 110 MB 进一步减少到 65 MB。

将 Elasticsearch 升级到版本 6.x，使我们的索引大小从 65 MB 进一步减少到 34.7 MB，这可能是由于大量稀疏数据的存在，并且 [Elasticsearch 6 有许多节省空间的改进](https://www.elastic.co/blog/minimize-index-storage-size-elasticsearch-6-0)。

#### 结论:

随着所有优化的就绪，在升级到 Elasticsearch 6.x 后，我能够将大小为 7.1 GB 的索引减少到 34.7 MB，并将 18k 大文件的索引时间从 1 小时减少到 50 秒。

从 Elasticsearch 2 到 6 的版本升级只给我们节省了 30 MB，因为所有的优化都已经在 elastic search 2 版中实现了，它能给我们大约 65 MB。

索引大小从 7.1 GB。因此，对于要实施的这些优化，版本升级是首选步骤，但不是强制步骤。

虽然大多数优化是工具特定的，但可以得出一个通用结论，即了解您的数据。

附言

弹性搜索的升级应通过[移除映射](https://www.elastic.co/guide/en/elasticsearch/reference/6.0/removal-of-types.html)中的`_type`字段来处理。

我也活跃于 StackOverflow 社区，过去主要回答弹性搜索问题。

***系列其他帖子***

*   [了解你的数据(KYD) - Sumit Kumar - Medium](https://dev.to/sumitkumar1209/know-your-data-kyd-39a6)
*   [优化电子商务数据- Sumit Kumar - Medium](https://dev.to/sumitkumar1209/optimising-e-commerce-data-2nna)
*   [优化基于文档的存储-了解您的数据(KYD)](https://dev.to/sumitkumar1209/optimising-document-based-storage---know-your-data-kyd-1hp8)

* * *