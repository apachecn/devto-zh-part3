# 充分利用服务器端缓存

> 原文：<https://dev.to/erikbooij/getting-the-most-out-of-server-side-caching-35o7>

不久前，我发现我和我的团队正在我们的电子商务网站上实施一个相当繁重的运行时操作。它涉及到 Elasticsearch 中的 [percolator](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-percolate-query.html) 功能(我计划写一篇关于我们如何使用它以及我们如何优化它的性能的文章，所以如果你想了解更多信息，请点击下面的关注)。

[![erikbooij image](img/6474e1700aa4d3fb10024fee3d629796.png)](/erikbooij)

## [内容布林](/erikbooij)

[I like tackling performance problems, experimenting with new tools and technologies and improving software a little bit every day.](/erikbooij)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)特殊运算](https://twitter.com/ErikBooij) [ ![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)特殊运算](https://github.com/ErikBooij)

当开发软件时，性能是或者应该总是一个关注点，但是当在大型电子商务平台上工作时，性能变化的结果变得非常明显，因为我们可以立即将转化率的降低以及由此造成的收入损失与页面加载时间的增加相关联。在 Elasticsearch 中运行过滤查询本质上是一项繁重的、在性能方面“昂贵”的操作，所以这给了我们更大的动力去专注于保持它的速度。

一旦我们实现了这个特性，并为我们的内部用户将它“激活”在一个特性开关之后，没有任何缓存，我们看到这个特性按预期工作，并且运行查询所花费的时间是*可接受的*。然而，我们确实注意到这在一定程度上给我们的弹性搜索集群带来了压力。没有什么特别令人担忧的，但我们应该可以做得更好。

# 优化我们的缓存使用

当将项目写入我们的缓存时，我们通常会考虑*我们需要它们有多新*。例如，产品价格或库存状态不能缓存超过几分钟，但产品名称可以安全地缓存一个小时或更长时间，因为后者不像前者那样可能发生变化。认识到您正在处理什么类型的数据以及这些数据的*易变性*，是决定应该在缓存中保存多长时间的良好开端。

在我们的案例中，我们缓存的许多数据都是基于我们商业团队中的同事所做的工作。添加新产品、改变网站的导航结构、编写内容等。在我们公司，工作日通常不在上午 8 点之前开始，在下午 6 点之前结束。这对我们的缓存策略来说是有价值的输入，因为这意味着我们的 Elasticsearch 查询结果在下午 6 点到第二天早上 8 点之间不会改变。或者在星期五下午 6 点到星期一早上 8 点甚至 T1 点之间。

我们开始将此作为确定缓存 TTL 的输入:

*   在**星期二晚上 8 点**将查询结果存储在缓存中？第二天早上 8 点过期**。**
*   在**周六上午 11 点**将查询结果存储在缓存中？在周一早上 8 点过期**。**

这就是我们的缓存 TTLs 现在的样子，横轴显然是时间，纵轴是我们允许项目在缓存中存在的秒数。

[![Dynamic cache TTLs](img/c5ae0f8e1c529a82ca8e2d29ed00b968.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lXrW0bqz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s0xdnzr5soamn7aloghp.png)

注意到模式了吗？每天晚上，缓存 TTL 都会大幅上升，而每周五晚上，TTL 会进一步上升。底部的扁平线是我们在工作日期间默认的 3600 秒 TTL，这是使用缓存和允许网站上快速弹出更改之间的平衡。

那么这对我们的 ***缓存命中率*** 有什么影响呢？

[![Cache hit percentage](img/42d68614d5fab07ecaded85a85efd9fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FIn35Z3O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q7rogl12vu3oq5i6aqvp.png)

如你所见:

*   在工作日的晚上，缓存命中率从大约 **65%** 增加到大约 **85%**
*   在周末，我们通常会看到缓存命中率在 **90%** 和 **100%** 之间。

# 学问

最初我们错过了一个相当关键的点。很容易说，所有这些项目下周一上午 8 点到期。然而，如果我们考虑的时间稍微长一点，我们就会意识到这会导致**大容量缓存驱逐**恰好在**的那个时间**。为了防止这种情况，并突然使这个缓存完全变冷，我们向到期添加了一些“模糊化”(因为没有更好的词)，并在实际阈值的两侧半小时内开始到期。

# 入门

我已经创建了一个很小的 PHP 库，您可以用它来开始做同样的事情，尽管这个概念显然不局限于 PHP，将相同的功能移植到任何其他语言应该是微不足道的。可以通过 composer:
轻松安装

```
$ composer require erikbooij/cache-scheduler 
```

来源可以在这里查看:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)T1】ErikBooij/[PHP-cache-scheduler](https://github.com/ErikBooij/php-cache-scheduler)

### 一个微型库，用于优化缓存 TTL，以应对数据本身过时的情况。

<article class="markdown-body entry-content container-lg" itemprop="text">

这个*缓存调度器*是一个库，允许你根据自己定义的时间表改变你的缓存 TTL。我已经为[写了一篇博文，解释了为什么你可能会考虑使用](https://dev.to/erikbooij/getting-the-most-out-of-server-side-caching-35o7)。

用法很简单:

1.  用 Composer 安装它:

    ```
    $ composer require erikbooij/cache-scheduler
    ```

2.  创建计划和调度程序:

    ```
    $schedule = (new Schedule)
        ->requireUpToDateDataFrom(Schedule::MON, 8, 0)
        ->allowStaleDataFrom(Schedule::MON, 17, 30)
        ->requireUpToDateDataFrom(Schedule::TUE, 8, 0)
        ->allowStaleDataFrom(Schedule::TUE, 17, 30)
        ->requireUpToDateDataFrom(Schedule::WED, 8, 0)
        ->allowStaleDataFrom(Schedule::WED, 17, 30)
        ->requireUpToDateDataFrom(Schedule::THU, 8, 0)
        ->allowStaleDataFrom(Schedule::THU, 17, 30)
        ->requireUpToDateDataFrom(Schedule
    ```

    …

</article>

[View on GitHub](https://github.com/ErikBooij/php-cache-scheduler)

您可以使用它来创建时间表并计算 TTL:

```
// Create the schedule using
//    ->requireUpToDateDataFrom(int $dayofTheWeek, int $hour, int $minute)
//    ->allowStaleDataFrom(int $dayofTheWeek, int $hour, int $minute)
$schedule = (new Schedule)
    ->requireUpToDateDataFrom(Schedule::MON, 8, 0)
    ->allowStaleDataFrom(Schedule::MON, 17, 30)
    ->requireUpToDateDataFrom(Schedule::TUE, 8, 0)
    ->allowStaleDataFrom(Schedule::TUE, 17, 30)
    ->requireUpToDateDataFrom(Schedule::WED, 8, 0)
    ->allowStaleDataFrom(Schedule::WED, 17, 30)
    ->requireUpToDateDataFrom(Schedule::THU, 8, 0)
    ->allowStaleDataFrom(Schedule::THU, 17, 30)
    ->requireUpToDateDataFrom(Schedule::FRI, 8, 0)
    ->allowStaleDataFrom(Schedule::FRI, 17, 30);

// Create the scheduler, passing it a SystemClock instance to interface with system time
$scheduler = (new Scheduler(new SystemClock))
    ->setSchedule($schedule)
    ->setExpirationSpread(ExpirationSpread::minutes(30));

// Use ->calculateTimeToLive(int $defaultTTL) to get the applicable TTL
$cache->set('cache-key', 'cache-value', $scheduler->calculateTimeToLive(3600)); 
```

schedule 和 expiration spread 既可以附加到调度器，例如当您想从 DI 容器提供缓存调度器时，也可以传递给`->calculateTimeToLive()`方法，如果它们的值取决于执行位置的话。如果两者都使用，后者将覆盖前者。

# 结论

只需很少的努力，就有可能延长一些缓存条目的寿命。这完全取决于您正在缓存什么数据，以及业务流程是如何围绕这些数据形成的。我很想听听你对这个想法和实施的看法。