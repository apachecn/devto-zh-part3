# 我如何比 Prisma 更快地制作 PostGraphile:一年后

> 原文：<https://dev.to/graphile/how-i-made-postgraphile-faster-than-prisma-1-year-later-4ead>

### 这在一定程度上归功于节点 12 令人难以置信的性能提升

去年 5 月，我发布了《我如何在 8 小时内让 PostGraphile 比 Prisma 更快》,以揭穿 Prisma 整合到他们营销网站中的极具误导性的图表。

<figure>[![](img/8974e6192f136e164aacb56eca614021.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iEhFyzL_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a14p95pgd0g1pprwmozy.gif) 

<figcaption>我希望我有预算用相当漂亮的设计和动画揭穿这张图表。</figcaption>

</figure>

PostGraphile 关注的是 GraphQL 查询的性能，这种查询是您在按照最佳实践构建 web 应用程序时会看到的，即单个 GraphQL 查询为一个单独的 web 页面提取所有需要的数据。Prisma 对一个不切实际的小查询(`byArtistId`，见下文)进行了基准测试，这实际上意味着他们在对 HTTP 层而不是 GraphQL 解析本身进行基准测试。一点友好的竞争对生态系统有好处，我还没有优化 PostGraphile 中的 HTTP 层，所以这是一个很好的理由，可以在那周晚些时候留出一天来做一些性能工作。即使在这个微不足道的小查询上，PostGraphile 也很快打败了 Prisma 我喜欢优化！

> ![Benjie 🐘 profile image](img/e12333ec3c963923d64e8b90eb9a3be0.png)本杰🐘[@ benjie](https://dev.to/benjie)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)所以我们在玩合成基准测试游戏是吗？😈😁2018 年 5 月 17 日上午 10:40[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=997064259220770816)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=997064259220770816)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=997064259220770816)3

六个月后，Prisma 让我知道他们已经把图表取下来了，并且显著提高了他们自己的表现。他们要求我重新运行基准测试。作为一个[众筹的开源开发者](https://www.graphile.org/sponsor/)，花了一段时间才找到更多的时间来分配给性能和基准测试工作。

在[PostGraphile 4.4](https://www.graphile.org/news/postgraphile-version-4-4/)发布之后，作为对 Node 12 发布的庆祝，我允许自己花一些时间深入研究 Node 的开发工具，寻找我们的性能可以进一步改进的地方。在这方面非常有用。

Node 12 本身带来了一些令人印象深刻的性能提升，它还开放了对现代 JavaScript 特性的更广泛支持，允许我们告诉 TypeScript 编译到更新的 ECMAScript 目标，并利用各种性能提升，而不必多填充表达性语法。为了保持向后兼容性，这些优化是通过`GRAPHILE_TURBO`环境变量选择加入的。Node 12 还带来了一个新的 HTTP 解析器`llhttp`，它显然也快了一点。总而言之，仅仅通过更改一些编译器标志和使用新的 Node.js 版本，我们就获得了很大的性能提升！

在 PostGraphile 的代码库中，我们设法在一些地方挤出了更多的性能。我将很快为 Node.js 开发人员发布一个帖子，解释我们到底做了什么([注册我们的邮件列表](http://eepurl.com/gfV9LD)以获得关于这个和其他图形新闻的通知)，但主要的事情是减少我们代码的垃圾收集开销，执行更多提前计算，以及自动跟踪和重用 PostgreSQL 准备好的语句。

<figure>[![](img/c76909d07b3f57541cf86bb528a3cb10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Ty--onX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aq2tpiyw09ni2d1yt4ay.png) 

<figcaption>从大约 6:28:33 到大约 6:30:18，我们针对 PostGraphile 的一个插装实例运行了 200，000 个请求，并使用 [node-clinic](https://github.com/nearform/node-clinic) 跟踪内存使用情况(顺便说一下，这非常酷)。这个“锯齿”图显示了 Node 定期运行垃圾收集(GC)来释放不再使用的内存。</figcaption>

</figure>

在这些优化之后，我重新运行了基准测试，测试了最新版本的 Prisma (1.32)，在节点 10 上运行的 postgrapile 4 . 0 . 0，以及在启用了`GRAPHILE_TURBO`的情况下在节点 12 上运行的 postgrapile 4 . 4 . 1 的 alpha。我们对基准所做的唯一重大改变是减少预热并发性(参见下面的`albums_tracks_genre_all`了解原因)。

故事讲完了——给我们看看数字！T3】

在[去年的图](https://graphql-bench-visualizer.now.sh/)中，最新版本的 PostGraphile(标签为`postgraphile-next`，实际上是 [v4.0.0-beta.10](https://github.com/graphile/postgraphile/releases/tag/v4.0.0-beta.10) )是粉红色的。postgrapile v 4 . 0 . 0 有一个与这个版本相似的性能配置文件，所以我们在新的图形中使用了粉红色[作为参考。我们为最新版本添加了一个新的绿色行:`postgraphile@alpha` (v4.4.1-alpha.4)。](https://graphql-bench-visualizer2.now.sh/)

我还在延迟图表中添加了叉号，以指示 0.1%或更多的请求失败的时间(并用失败请求的百分比标记叉号)，因为这是一个重要的指标，如果没有交叉引用相关的“成功的请求”图表，以前是看不到的。此外，Y 轴已被扩展以显示稍高的延迟范围。

接下来是 5 个基准测试查询中每一个的部分。基准设置和去年几乎一模一样，就不再赘述了(参见[去年帖子](https://medium.com/graphile/how-i-made-postgraphile-faster-than-prisma-graphql-server-in-8-hours-e66b4c511160)的“基准”部分)。

#### prisma_deeplyNested

这个查询显示了各种软件如何处理涉及大量数据库表、关系和列的查询。Prisma 将这个请求命名为“深度嵌套”,但是对于一个面向前端的 GraphQL API 来说，处理类似的查询并不罕见。

```
query prisma_deeplyNested {
  allAlbumsList(condition: {artistId: 127}) {
    albumId
    title
    tracksByAlbumIdList {
      trackId
      name
      genreByGenreId { name }
    }
    artistByArtistId {
      albumsByArtistIdList {
        tracksByAlbumIdList {
          mediaTypeByMediaTypeId { name }
          genreByGenreId { name }
        }
      }
    }
  }
} 
```

<figure>[![](img/7abe86c54b9eb601f668e4ff1395816d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3GE0TYD1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0z4co0litdiwwfa4ysf6.png) 

<figcaption>此图表显示了各种软件在开始丢弃请求(或未达到 5s 延迟截止时间)之前每秒可以处理的请求数。Prisma 从去年的 100rps 增加到 225 RPS——显著增加。PostGraphile 现在更优雅地处理更高的负载——尽管我们在 700rps 后开始丢弃请求，即使在 800rps 我们也只丢弃了 2.26%。</figcaption>

</figure>

<figure>[![](img/589939718e6a73cc33045ff3ec026918.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jg0RLSpu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w3vmlqu5kwlaylpmtgoc.png) 

<figcaption>这张 95%延迟图表显示了不同负载下各种软件响应请求的速度。最新的 PostGraphile 已经全面降低了延迟，并且在超过 600rps 的峰值后仍然表现良好。</figcaption>

</figure>

#### 专辑 _ 曲目 _ 流派 _ 全部

去年我们不得不排除这个查询，因为我们没有从 Prisma 得到任何结果，也不知道为什么。今年我们发现了这个问题:Prisma 在热身阶段变得不知所措，在主要基准测试开始时无法做出反应。解决方案是将 5 分钟预热期间的并发性从 100rps 减少到 10rps(您可以在去年的帖子中了解为什么预热是必要的)。

该查询显示了从数据库的特定集合中获取所有行，以及一些相关记录。通常，像这样的前端 GraphQL 请求应该在根级别进行分页(例如，一次限制为 50 个相册)，但是因为相册表中只有 347 行，所以这并不太糟糕。这个查询更好地代表了您可能从后端而不是从 web 前端进行的 GraphQL 查询。

```
query albums_tracks_genre_all {
  allAlbumsList {
    albumId
    title
    tracksByAlbumIdList {
      trackId
      name
      genreByGenreId {
        name
      }
    }
  }
} 
```

<figure>[![](img/81560b7faa835d03955abbaa2fa1a28d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bIUVyBY---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/az5qznbw7qr5usnb09d9.png)

<figcaption>postgrapile 从 70rps 提高到 80rps 而不丢弃任何请求。</figcaption>

</figure>

<figure>[![](img/b0b1ba0c4f9b9ef184082807ae7425a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EtOnxWX5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zqigo3kprmp4eermdofg.png) 

<figcaption>即使是 80rps，PostGraphile 也能在 250 毫秒内处理 95%的请求。最新的 postgraphile@alpha 显示出延迟的显著减少和吞吐量的增加。</figcaption>

</figure>

#### 专辑 _ 曲目 _ 流派 _ 一些

这个查询与前一个查询几乎相同，只是通过过滤特定的艺术家减少了结果的数量(从 347 个减少到 3 个)。这是一个简单的前端 GraphQL 查询的相当好的例子。

```
query albums_tracks_genre_some {
  allAlbumsList(condition: {artistId: 127}) {
    artistId
    title
    tracksByAlbumIdList {
      trackId
      name
      genreByGenreId {
        name
      }
    }
  }
} 
```

<figure>[![](img/fcbdf56788522b6628278d073b6e27f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qrC-jcCT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/18n4dp9smdh365nx0vrf.png) 

<figcaption>Prisma 大幅提升——从去年的 300rps 提升到今年的 800rps。postgrapile 从 1500rps 增加到 1700rps，并且更优雅地处理超过其最大值的情况:在 1800rps 时，postgrapile 仅丢弃 0.7%的传入请求。</figcaption>

</figure>

<figure>[![](img/b6996f900c1ab7337693555c08b48968.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qLBgW10I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v4rysbafborvjyzjoogx.png)

<figcaption>PostGraphile 的延迟比以往任何时候都低，在 50 毫秒内以 1700rps 的速度满足 95%的请求。</figcaption>

</figure>

#### 城市艺术时间

这个查询非常简单，只需要从数据库的一行中请求两个字段。在一个重要的应用程序的 web 前端，很少会有如此简单的 GraphQL 请求——它显示了 HTTP 层的底层性能，而不是 GraphQL 解析本身。

```
query artistByArtistId {
  artistByArtistId(artistId: 3) {
    artistId
    name
  }
} 
```

<figure>[![](img/a2915863059407a7c39ca2df6275ba8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G21aDXj9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hiucjbszte10r2xi9fvn.png) 

<figcaption>这张图看起来和去年惊人的相似，除了一切都向右移动了！PostGraphile 现在可以处理令人印象深刻的 3700rps(相比去年的 3000rps)而不丢弃任何请求，并且达到 4000rps(相比去年的 3200rps)而只丢弃 0.8%的请求。Prisma 也有所改进，从 2750rps(没有放弃请求)提高到 3600rps。</figcaption>

</figure>

<figure>[![](img/7b9997b78528abfe3983414911204882.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uGCZwFUL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1xg3uboe4fkrhib5ban3.png) 

<figcaption>表示失败百分比的叉号确实有助于解释这里的延迟结果；在 3000rps 之后，Prisma 设法在短时间内实现比 PostGraphile 稍低的延迟，直到他们开始以 3700rps 丢弃请求。这可能是由于 Scala(在 JVM 上运行)和 Node.js 处理垃圾收集的方式不同。</figcaption>

</figure>

#### 曲目 _ 媒体 _ 首发 _20

为了完整起见，这个查询请求单个数据库表中 20 行中的 2 列——就像一个稍微大一点的 byArtistId 版本。来自网页的 GraphQL 请求很少这么简单。

```
query tracks_media_first_20 {
  allTracksList(first: 20) {
    trackId
    name
  }
} 
```

<figure>[![](img/840c2354e7b7061644625ddae8f47268.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9xdKybU_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wt069fda2w77p1aklcal.png) 

<figcaption>Prisma 从去年的 2000rps 提升到今年的 3000rps。PostGraphile 以 3500rps 增加了领先优势。</figcaption>

</figure>

<figure>[![](img/c8bcfad83a898161b56ccc48e5f8b03d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mFfkI2UI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r21g8j1632tn9tyvzu2v.png) 

<figcaption>同样，该图显示了 Node.js 与 Scala 在垃圾收集、HTTP 开销和其他相关成本方面的差异，Node 实现了更高的吞吐量，但在高并发时延迟稍高。
</figcaption>

</figure>

#### 速度真的那么重要吗？

是也不是。我做优化是因为这是一个有趣的挑战，看看我可以用一种解释型语言把计算机推进多远，而不用把我的代码弄得太乱。PostGraphile 的用户现在将从更快的性能和更满意的最终用户中受益，只需更新到最新版本即可——他们根本不需要修改任何代码。我想那真的是 cool✨

但是性能不是一切——我们在 PostGraphile 关注的事情之一是可扩展性。我们的工作不是简单地将您的数据库从 SQL 转换成 GraphQL。我们的工作是帮助您尽快构建理想的 GraphQL API。为此，我们尽可能地为您做了大量的样板工作，但随后我们会为您提供添加、定制和定制 GraphQL 模式的方法。我们根本不相信我们的工作是向您的最终用户公开数据库的所有功能；相反，我们认为我们应该允许您利用数据库的功能来构建您的前端开发人员需要的 GraphQL API，而无需他们担心复杂的连接、子查询、公共表表达式、`ON CONFLICT DO UPDATE`、索引、SQL 查询优化以及其他类似的事情。尽管 PostGraphile 具有可扩展性和灵活性，但它还是获得了令人难以置信的良好性能，这在一定程度上要归功于选择 Node.js 作为开发平台。

#### 那么接下来呢？

你现在就可以用`yarn install postgraphile@alpha`来体验一下新的海报。它通过了所有的测试，但还没有完全通过社区的审查，因此有了“alpha”标签——如果你试用它，请在我们的 Discord chat 上给我们留言[,让我们知道你的进展如何！](http://discord.gg/graphile)

> `yarn install postgraphile@alpha`

如果你欣赏我们的工作，请[赞助我们](https://www.graphile.org/sponsor/)——我们非常感谢我们的 Patreon 赞助商，他们帮助我们不断前进。

> 如果您欣赏我们的工作，请[赞助我们](https://www.graphile.org/sponsor/)。

感谢您的阅读，我将很快在 Node.js 性能优化上发布另一篇文章，这是我用来实现这一点的— [注册我们的邮件列表](http://eepurl.com/gfV9LD)以获得关于这一点和其他图形新闻的通知。

> [注册我们的邮件列表](http://eepurl.com/gfV9LD)