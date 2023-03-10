# MongoDB 模式设计模式-属性

> 原文：<https://dev.to/kenwalger/mongodb-schema-design-patterns--attribute-52j4>

欢迎回到 MongoDB 模式设计模式系列。上次我们看到了多态模式，它涵盖了集合中所有文档结构相似但不完全相同的情况。在这篇文章中，我们将看看属性模式。属性模式特别适用于以下情况:

*   我们有包含许多相似字段的大文档，但是有一个具有共同特征的字段子集，我们希望对该字段子集进行排序或查询，*或*
*   我们需要排序的字段只存在于一小部分文档中，*或*
*   上述两个条件在文档中都得到了满足。

出于性能原因，为了优化我们的搜索，我们可能需要许多索引来考虑所有的子集。创建所有这些索引可能会降低性能。属性模式为这些情况提供了一个很好的解决方案。

#### 属性模式

我们来考虑一个电影集合。这些文档很可能在所有文档中包含相似的字段:标题、导演、制片人、演员等等。假设我们想在发布日期进行搜索。我们在这样做时面临的一个挑战是*哪一个*发布？电影经常在不同的国家不同的日期上映。

```
{
    title: "Star Wars",
    director: "George Lucas",
    ...
    release_US: ISODate("1977-05-20T01:00:00+01:00"),
    release_France: ISODate("1977-10-19T01:00:00+01:00"),
    release_Italy: ISODate("1977-10-20T01:00:00+01:00"),
    release_UK: ISODate("1977-12-27T01:00:00+01:00"),
    ...
} 
```

搜索发布日期需要同时查看许多字段。为了快速搜索上映日期，我们需要电影收藏的几个索引:

```
{release_US: 1}
{release_France: 1}
{release_Italy: 1}
... 
```

通过使用属性模式，我们可以将这个信息子集移动到一个数组中，并减少索引需求。我们将这些信息转换成一组键值对:

```
{
    title: "Star Wars",
    director: "George Lucas",
    …
    releases: [
        {
        location: "USA",
        date: ISODate("1977-05-20T01:00:00+01:00")
        },
        {
        location: "France",
        date: ISODate("1977-10-19T01:00:00+01:00")
        },
        {
        location: "Italy",
        date: ISODate("1977-10-20T01:00:00+01:00")
        },
        {
        location: "UK",
        date: ISODate("1977-12-27T01:00:00+01:00")
        },
        … 
    ],
    … 
} 
```

通过对数组中的元素创建一个索引，索引变得更加易于管理:`{ "releases.location": 1, "releases.date": 1}`

通过使用属性模式，我们可以将组织添加到文档中，以获得共同的特征，并考虑罕见/不可预测的字段。比如新的或者小的节日上映的电影。此外，转向键/值约定允许使用非确定性命名，并且可以轻松添加限定符。例如，如果我们的数据收集是在水瓶上，我们的属性可能看起来像这样:

```
"specs": [
    { k: "volume", v: "500", u: "ml" },
    { k: "volume", v: "12", u: "ounces" }
] 
```

在这里，我们将信息分解为键和值“k”和“v ”,并添加第三个字段“u ”,它允许单独存储度量单位。

#### 示例用例

属性模式非常适合包含具有相同值类型的字段集的模式，例如日期列表。它在处理产品特性时也能很好地工作。一些产品，如服装，可能有以小号、中号或大号表示的尺寸。同一系列中的其他产品可以用体积表示。还有一些可以用物理尺寸或重量来表示。

资产管理领域的一个客户最近使用属性模式部署了他们的解决方案。客户使用该模式来存储给定资产的所有特征，这些特征在资产之间很少是共同的，或者在设计时很难预测。关系模型通常使用复杂的设计过程，以[用户定义字段](https://martinfowler.com/bliki/UserDefinedField.html)的形式表达相同的想法。

虽然产品目录中的许多字段是相似的，例如名称、供应商、制造商、原产国等。项目的规格或属性可能会有所不同。如果您的应用程序和数据访问模式依赖于一次搜索这些差异，那么属性模式为数据提供了一个很好的结构。

#### 结论

属性模式提供了更简单的文档索引，针对每个文档的许多相似字段。通过将这个数据子集移动到一个键-值子文档中，我们可以使用不确定的字段名称，向信息中添加额外的限定符，并更清楚地陈述原始字段和值的关系。当我们使用属性模式时，我们需要更少的索引，我们的查询变得更容易编写，我们的查询变得更快。

我们要讨论的下一个模式是 Bucket 设计模式。

如果你有问题，请在下面留下评论。

* * *

*这篇文章最初发表在 [MongoDB 博客](https://dev.to/mongodb/building-with-patterns-the-attribute-pattern-299m-temp-slug-2892504)上。*

[![Facebook](img/2507e41253789bf875d2c97fa445d9bc.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodb-attribute-design-pattern%2F&t=MongoDB%20Schema%20Design%20Patterns%20%E2%80%93%20Attribute&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodb-attribute-design-pattern%2F&p%5Bimages%5D%5B0%5D=&p%5Btitle%5D=MongoDB%20Schema%20Design%20Patterns%20%E2%80%93%20Attribute)[![twitter](img/edfe176328955ca1f93fb420bd02a8aa.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodb-attribute-design-pattern%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodb-attribute-design-pattern%2F)[![reddit](img/3608b650c6387ec5b764ff868b337be5.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodb-attribute-design-pattern%2F&title=MongoDB%20Schema%20Design%20Patterns%20%E2%80%93%20Attribute)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodb-attribute-design-pattern%2F&title=MongoDB%20Schema%20Design%20Patterns%20%E2%80%93%20Attribute)[![mail](img/15358a520f2d88aabf870e0f3ce8f424.png "Share by email")](mailto:?subject=MongoDB%20Schema%20Design%20Patterns%20%E2%80%93%20Attribute&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fmongodb-attribute-design-pattern%2F)

帖子 [MongoDB 模式设计模式-属性](https://www.kenwalger.com/blog/nosql/mongodb/mongodb-attribute-design-pattern/)最早出现在肯·w·阿尔杰的[博客上。](https://www.kenwalger.com/blog)