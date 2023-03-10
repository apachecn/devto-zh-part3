# 使用 DataDrivenJS 进行更智能的列表排序

> 原文：<https://dev.to/anndd/smarter-list-sorting-with-datadrivenjs-cfn>

如果你在博客或网上商店工作，你会有很多需要排序的列表。帖子，产品，产品过滤器...你可能会从新到旧，从便宜到昂贵，或者按字母顺序排列它们。或者，你的老板告诉你“正确的”顺序，你手动安排项目。

然而，如果你看一下网站的统计数据，你会发现无论你使用什么样的顺序，它都不会与你最受欢迎或转化率最高的商品的顺序相匹配。这意味着用户必须看到/点击许多不太相关的项目，直到她或他偶然发现你的重磅炸弹。

如果我们可以在网站上众包列表顺序，而不是任意排序，会怎么样？

你可能认为这是另一个关于使用 R 或 Python 编写推荐引擎的教程，但事实并非如此。这也不是一个关于数据库或任何其他后端技术的教程。不，先生，我们只讨论前端，也就是无服务器 JavaScript，也就是 DataDrivenJS。

*什么是无服务器 JavaScript？*

它是一个托管的后端，您可以使用 JavaScript API 与之交互。

*什么是 DataDrivenJS？*

这几乎就像谷歌分析。它跟踪人们在你的网站上做了什么。但是它没有显示任何饼状图。相反， **DataDrivenJS 使前端开发人员能够非常容易地将跟踪的数据**读取回网站，并添加——顾名思义——数据驱动的功能。

下面是它有多简单。

[免费注册](https://datadrivenjs.com/signup.html)，在你的网站上添加跟踪代码，然后运行这样的代码，在每次用户查看帖子(或者:订购产品、点击导航项目等)时向你的帐户发送数据。):

```
DD.tracker.trackMetaEvent('viewed post','My Post About DataDrivenJS'); 
```

然后，用几行 JavaScript 代码，你可以读回这些数据([见文档](https://datadrivenjs.com/docs.html) ):

```
 // prepare public data feed

var feed = DD.data.feed('My Blog Posts');
feed.select(
  DD.data.datapoints.metaevent('viewed post'),
  DD.data.datapoints.metaevent('viewed post').count().as('views')
).orderBy(
  DD.data.feedColumn('views').desc()
);

// read the entire data feed
// curious what '{}' is for? I'll tell your later!

DD.reader.read(feed, {}, function(response){
  console.log(response.results);
}); 
```

你会得到一个你所有博客文章的列表，按`views`排序——从浏览次数最多到最少:

```
[
  {"viewed post":"My Post About Cats", "views":1000},
  {"viewed post":"My Post About DataDrivenJS", "views":50},
  {"viewed post":"My Post About Python", "views":10}
] 
```

上面的数据足以对已经显示在你的页面上的文章进行排序。

容易吗？但是，如果你有数百篇博客文章，而不是所有的文章都被一次加载，那该怎么办呢？

除了跟踪标题，您还可以跟踪 JSON 的所有细节:

```
DD.tracker.trackMetaEvent('viewed post',JSON.stringify({
  "Title":"My Post About DataDrivenJS",
  "URL":"https://dev.to/my-post-about-datadrivenjs/",
  "Thumbnail":"https://dev.img/my-thumbnail.png",
  "Category":"JavaScript"
})); 
```

然后，您将有足够的数据来创建一个带有缩略图的博客帖子列表。

你甚至可以过滤结果:

```
 // prepare public data feed

var feed = DD.data.feed('My Blog Posts');
feed.select(
  DD.data.datapoints.metaevent('viewed post'),
  DD.data.datapoints.metaevent('viewed post').count().as('views')
).orderBy(
  DD.data.feedColumn('views').desc()
);

// read only 5 most-viewed posts related to JavaScript

var query = DD.data.feedQuery().select(
  DD.data.feedColumn('viewed post')
).where(
  DD.data.feedColumn('viewed post').contains('javascript') // case insensitive
).limit(5);

DD.reader.read(feed, query, function(response){
  console.log(response.results);
}); 
```

你可能会说——“哦，没有 DataDrivenJS 我也能做到——我只需要一些 PHP 和 MySQL，我很好”。你说得对。通过一些努力，您可以在数据库中添加一列来存储每个项目的视图。但是，如果一个月后，你想改变排序方式，对移动用户和桌面用户的项目进行不同的排序，该怎么办呢？

有了 DataDrivenJS，你需要的数据已经在那里了，你所需要做的就是用移动访问的数据创建一个单独的 feed:

```
 // prepare public data feed

var feed = DD.data.feed('My Blog Posts for Mobile');
feed.select(
  DD.data.datapoints.metaevent('viewed post'),
  DD.data.datapoints.metaevent('viewed post').count().as('views')
).orderBy(
  DD.data.feedColumn('views').desc()
).from(
  DD.data.segment('Mobile Visits').where(
    DD.data.datapoints.property('screenWidth').isLessThanOrEqualTo(480),
  )
);

// read the entire feed

DD.reader.read(feed, {}, function(response){
  console.log(response.results);
}); 
```

此时你一定在想——“这个女生写了一个关于排序列表的教程，但是她实际上一个列表都没有排序过！”。没错，但我相信你会自己想出那部分的。

*如果你不愿意，这里有一个代码笔和一个工作示例——注意，添加到“资源”的脚本包含项目特定的参数。

[https://codepen.io/anndd/embed/vbxKeV?height=600&default-tab=result&embed-version=2](https://codepen.io/anndd/embed/vbxKeV?height=600&default-tab=result&embed-version=2)