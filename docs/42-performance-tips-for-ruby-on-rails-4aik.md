# Ruby on Rails 的 42 个性能提示

> 原文：<https://dev.to/mskog/42-performance-tips-for-ruby-on-rails-4aik>

这篇文章最初发布在我的博客上

* * *

因为 Ruby on Rails 不是最快的 web 框架，所以有时候你需要提高性能来跟上。这篇文章将列出 42 个我最喜欢的提高应用速度的技巧。

*如果你需要一台便宜而坚固的服务器来运行你的应用程序，那么请随意使用我的[推荐代码](https://hetzner.cloud/?ref=GjnpYTNYtgRU)在 Hetzner 获得 20 欧元的信用点数。阅读我的[评论](https://dev.to/mskog/hetzner-cloud-review-revisited-in-2020-2jii)了解更多信息。我使用 CX31 型号来解决所有这些问题，但您可能会得到更便宜的产品。*

## 我们开始吧！

#### 锤炼你的期望

Ruby 和 Ruby on Rails 并不以其性能著称。尤其是视图渲染非常慢，所以你不应该期望得到亚毫秒级的视图渲染，比如说你可以在 Elixir 中得到。一个好的基准是瞄准经典的[100 毫秒目标](https://www.speedshop.co/2015/05/27/100-ms-to-glass-with-rails-and-turbolinks.html)。

#### 使用 APM

拥有诸如平均页面加载时间、数据库中花费了多少请求等指标对于性能优化至关重要。新遗迹是一个很好的方法，但是对于一个副业来说，它相当昂贵。 [Skylight](https://www.skylight.io/) 是另一个选项，每月前 10 万个请求是免费的。

#### 使用机架式微型剖面仪

[rack-mini-profiler](https://github.com/MiniProfiler/rack-mini-profiler) 是获取有用指标的绝佳方式。安装后，它会在您的浏览器中显示一个标记，显示总请求时间。如果您单击它，您将获得每个视图以及运行的每个数据库查询的指标。它还会显示渲染模板花费了多少时间，以及在数据库中花费了多少时间。

#### 测量两次，优化一次

一定要确保你知道你在优化什么，为什么要优化。当问题
在数据库中时，进行视图优化没有什么意义。

#### 80/20 规则

也被称为[帕累托原理](https://en.wikipedia.org/wiki/Pareto_principle)。简单来说，就是说 80%的结果来自于 20%的原因。这里的要点是，当有更大的事情要解决时，你不应该把时间花在微优化上。例如，当视图呈现需要一秒钟来呈现该列表时，可能不值得花费数小时来将数据库查询再减少一毫秒。

#### 使用 Ruby 和 Ruby on Rails 的更新版本

这是不言而喻的，但是每个版本的 Ruby 性能都更好。确保使用最新版本。Ruby on Rails 6.0 刚刚进入测试版，例如内置了并行测试。绝对值得一探究竟！

#### 确保在生产模式下测量

例如，默认情况下，Rails 不会在开发环境中缓存东西，所以请确保使用生产环境或手动操作配置，以获得与生产应用程序相同的设置。

#### 在后台运行东西

使用 [Activejob](https://edgeguides.rubyonrails.org/active_job_basics.html) 和 [Sidekiq](https://github.com/mperham/sidekiq) 进行快速后台作业。不要在主线程中发送电子邮件等。把它卸给工人！

#### 进一步阅读和资源

[https://www.skylight.io/](https://www.skylight.io/)

[https://www . speed shop . co/2017/07/11/is-ruby-too-slow-for-web-scale . html](https://www.speedshop.co/2017/07/11/is-ruby-too-slow-for-web-scale.html)

[http://engineering . app folio . com/app folio-engineering/2018/12/13/a-short-update-how-fast-is-ruby-260 rc1](http://engineering.appfolio.com/appfolio-engineering/2018/12/13/a-short-update-how-fast-is-ruby-260rc1)

* * *

## 服务器和硬件

#### 使用单核性能良好的服务器

拥有一个高时钟频率的 CPU 将会给 Ruby on Rails 应用程序的性能带来奇迹。例如，在 [DigitalOcean](https://m.do.co/c/457e363d283d) 上，如果你使用优化的液滴而不是常规液滴，你可以获得时钟速度提高约 30%的 VPS。它们运行在大约 2.6GHz。如果你想要一个专用服务器，不需要 ECC RAM，那么你可以在 Hetzner 获得一个带有 [i7-8700k 的服务器。它的基本时钟速度为 3.7GHz，这将创造奇迹。再次强调，在做出可能不会有所改变的事情之前，请确保您对此进行了评估。](https://www.hetzner.com/dedicated-rootserver/matrix-ex)

#### 彪调

Puma 是 Ruby on Rails 从 5.0 版本开始的默认 web 服务器。默认情况下，它将启动单个工作进程，并使用线程来处理请求。为了获得最佳性能，您应该对此进行调整。一个好的起点是为服务器上的每个 CPU 内核配备一个工作线程。您可以在`config/puma.rb`中更改这一点，您正在寻找的是`workers`配置。请记住，每个工人是一个分叉的进程，这将使用大量的内存，所以要确保你不会用完。

#### 使用 HTTP2

如果你使用 Nginx，那么确保你启用了 HTTP2 以获得最佳性能。如果你使用 [Dokku](https://github.com/dokku/dokku) ，那么如果你的 Nginx 安装支持的话，它会自动完成。

#### 试试谷歌的 pagespeed 模块

谷歌为 Apache 和 Nginx 都提供了 pagespeed 模块。他们可以为你做各种各样的优化，但是我在这里推荐的是做内联 CSS 的那个。它所做的是试图找到正确呈现页面所需的 CSS 规则，然后内联该 CSS，而不是从外部文件加载它。这意味着客户端不必在页面呈现之前等待外部 CSS 加载，这可能会增加加载时间。你首先需要编译支持这个的 Nginx，然后在 http 块中配置看起来有点像这样:

```
pagespeed on;
pagespeed FileCachePath /var/ngx_pagespeed_cache;
pagespeed EnableFilters prioritize_critical_css;
pagespeed Domain  *.mydomain.com;
pagespeed InlineResourcesWithoutExplicitAuthorization Stylesheet; 
```

Enter fullscreen mode Exit fullscreen mode

#### 确保服务器没有交换

这是不言而喻的，但要确保你有足够的内存。Rails 非常需要内存，它会随着多个 Puma 进程和后台工作人员而快速增加。

#### 有服务器指标

您需要了解您的服务器运行情况。他们在交换吗？您目前有多少 CPU 负载等。我使用的工具是 Telegraf+Influxdb+Grafana。Telegraf 将从我的服务器收集指标，并将它们推送到 Influxdb。然后 Grafana 将为我绘制这个设置，甚至可以向 Slack 发送警告，例如，如果出现问题。

[![](img/e7e9960c7f5afa56a63c4f3f9d3c0c80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6biGWDQC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.mskog.cimg/grafana_metrics%402x.png)

#### 进一步阅读

[https://www.speedshop.co/2017/10/12/appserver.html](https://www.speedshop.co/2017/10/12/appserver.html)

[https://lkhill.com/telegraf-influx-grafana-network-stats/](https://lkhill.com/telegraf-influx-grafana-network-stats/)

* * *

## 数据库

#### 消除 N+1 个查询

这是一个经典问题。如果您用`user = User.first`加载一个用户，然后通过执行例如`user.comments.each...`来遍历评论，那么 Rails 将为每个评论执行一个查询。你可以通过做`user = User.includes(:comments)`来消除这一点。[子弹](https://github.com/flyerhzm/bullet)宝石可以自动为你发现这些问题。您也可以使用 rack-mini-profiler 来完成此任务，但 Bullet 更适合此任务。

#### 缺失数据库索引

上面的 rack-mini-profiler 可能会清除缓慢的查询，很多时候这是由于缺少索引或索引不理想造成的。为了容易地找到哪些索引丢失了，你可以使用 [lol_dba](https://github.com/plentz/lol_dba) gem。它将找到任何缺少索引的简单情况，我强烈推荐使用它作为基线。

#### 使用数据库卢克！

确保在方便的时候使用数据库。如果使用 Ruby，在内存中保存大数组，并对它们进行排序、分组等会占用大量 CPU 时间。数据库可以更快地完成这项工作，但有时您必须使用原始 SQL 来完成。如果您对 SQL 感到不舒服，或者只是不想使用它，除非万不得已，那么您可以跳过这个提示。然而，这将我们引向下一个问题....

#### 学习 SQL！

这是一个有争议的问题，但是我发现虽然你可以在不了解任何 Rails 中的 SQL 的情况下创建复杂的应用程序，但是你可能仍然想学习如何编写 SQL 查询。如果你知道你在做什么，你可以通过使用 SQL 做一些很棒的优化或者某些事情。如果你知道如何使用[解释命令](https://www.postgresql.org/docs/9.1/sql-explain.html)之类的事情，你也将能够找出**为什么**active record-query 如此之慢。

#### 当心分页

如果您的查询很慢，那么分页会是一个很大的问题，尤其是当您在查询中进行分组时。通过修改不包括行数的分页方式，通常可以获得良好的结果。如果分页不需要计算总行数，那么这通常会显著提高性能。你还应该看看比其他解决方案快得多的现代 Pagy gem。

#### 加速 Postgres 计数

如果您有大量的数据，那么简单地计算行数可能会有问题，特别是当它是一个大型查询时。你可以使用[计数估计](https://wiki.postgresql.org/wiki/Count_estimate)来解决这个问题，但是使用起来有点痛苦。

#### 急切或预载的查询

您应该始终使用`includes`来提高 ActiveRecord 中关系的性能。然而，这将总是至少一个额外的查询。您可以通过使用`eager_load`来避免这种情况，它将生成一个单独的查询。但是，这并不总是更快，而且会导致大量内存使用。在使用它之前，请仔细进行基准测试。更多细节你可以阅读[这篇文章](http://blog.scoutapp.com/articles/2017/01/24/activerecord-includes-vs-joins-vs-preload-vs-eager_load-when-and-where)

#### 使用适当的索引进行全文搜索

例如，如果你只是在 Postgres 中做简单的`ILIKE '%foobar%'`，那么当有大量数据需要搜索时，速度会非常慢。如果你为 Postgres 设置了合适的索引，那么对于全文搜索来说，postgres 可以是 [**非常**高效的](http://rachbelaid.com/postgres-full-text-search-is-good-enough/)。你也可以使用 [pg_search gem](https://github.com/Casecommons/pg_search) 来使这变得更容易。我个人觉得用 [Searchkick gem](https://github.com/ankane/searchkick) 的 [Elasticsearch](https://www.elastic.co/) 更容易使用。但是，不要在需要全文搜索时立即使用它。维护是另一回事，使用这种解决方案还有其他缺点。

#### 不要使用 ActiveRecord 插入大量数据

例如，如果你只是在一个巨大的 JSON 博客上循环，并使用 ActiveRecord 插入记录，那么你可能会发现它非常慢。我推荐使用 [activerecord-import gem](https://github.com/zdennis/activerecord-import) 来加快速度。

* * *

## 缓存

让代码更快的最好方法是根本不运行它。所以缓存数据和执行在任何语言中都是有意义的，尤其是像 Ruby 这样表现不佳的语言。这里有很多内容要介绍，所以让我们开始吧。

#### 使用合适的缓存存储

缓存存储是 Rails 应用程序中存储缓存数据的地方。默认情况下，将缓存存储在文件系统中，这对于生产应用来说太慢了。您可以使用`ActiveSupport::Cache::MemoryStore`拥有一个进程内内存缓存存储，但是它不能在进程间共享，并且不能在重启后继续存在。一个生产 Rails 应用程序很可能会有多个进程为请求提供服务，例如在集群模式下使用 Puma，拥有一个不在进程间共享的缓存是非常糟糕的。我的建议是使用 [Memcache](https://guides.rubyonrails.org/caching_with_rails.html#activesupport-cache-memcachestore) 或者 [Redis](https://guides.rubyonrails.org/caching_with_rails.html#activesupport-cache-rediscachestore) 。

如果您使用 Sidekiq 进行后台工作，那么您已经有一个 Redis 服务器可用，因此您可以将它用作缓存。但是，如果您与 Sidekiq 共享 Redis 实例进行缓存，那么要小心。Redis 很容易被缓存数据填满，没有给 Sidekiq 作业留下空间。这在 Redis 4 中通过使用`volatile-lfu`驱逐策略很容易解决。它将使用智能算法剔除可能的最佳数据。因为所有的缓存都设置了 volatile 标志，所以如果需要的话，它会删除缓存的数据，为 Sidekiq 作业腾出空间。

#### 使用 HTTP 缓存但要小心

HTTP 缓存有点复杂，所以你真的应该读一篇关于它的专门文章。我推荐在 [Heroku](https://devcenter.heroku.com/articles/http-caching-ruby-rails#conditional-cache-headers) 的那个。只要数据不是完全新的，使用`expires_in`可能是一个非常强大的工具。例如，博客中的文章列表不一定需要在每次请求时都保持新鲜。在这个基础上设置一个 5 分钟的缓存，再加上一个代理服务器和/或一个 CDN，真的可以加快速度，特别是当你在一个 API 中使用它的时候，这样你就不用担心会话了。

#### 页面和动作缓存

Rails 4.0 中删除了页面和动作缓存。页面缓存保存了整个 HTTP 响应，下一个甚至根本没有命中 Rails 堆栈。动作缓存命中控制器，但如果找到缓存的数据，则不执行控制器动作。您可以使用[action pack-action _ caching gem](https://github.com/rails/actionpack-action_caching)在 Rails 4+中使用它。

页面缓存主要是缓存静态页面，比如 404 或 500 页面，也许还有应用程序中一些长期存在的静态页面。这对于其他事情来说太麻烦了，因为它会忽略任何查询参数。

动作缓存非常强大，因为它非常快。您也可以使用`cache_path`选项来控制缓存行为。例如，您可能希望只缓存 JSON 响应，或者只缓存未登录到您的应用程序的用户的数据。您也可以在这里使用片段缓存一节中描述的缓存键。它比片段缓存快得多，所以我建议尽可能使用它，尤其是对于像博客或 CMS 这样的静态应用程序。

#### 片段缓存

这也被称为“俄罗斯娃娃缓存”。这是通过使用`cache`方法在视图模板中完成的。您可以缓存视图的每个图层。例如，如果你有一个包含多篇文章的博客，那么你可以使用类似于`render partial: 'posts/post', collection: @posts, cached: true`的东西来缓存文章的渲染。Rails 将缓存每个帖子及其自己的关键字，以及一个在任何帖子发生变化时也会发生变化的关键字。当没有任何变化时，它将通过一次对缓存的调用来获取缓存，如果一个帖子发生了变化，它将只重新呈现该帖子。其他的将像平常一样从缓存中取出。

然后，您可以使用`cache`块来嵌套这些缓存，例如缓存每个帖子的评论或每个帖子中昂贵的部分渲染。由于 Ruby on Rails 视图渲染非常慢，因此使用这种技术确实可以加快速度。

#### 原始缓存

另一种缓存方式是使用`Rails.cache.fetch`方法。这将缓存给它的块的结果，如下:

```
Rails.cache.fetch('my_cache_key', expires_in: 1.hour) do
  SomeApi.fetch_posts
end 
```

Enter fullscreen mode Exit fullscreen mode

下一次使用相同的缓存键运行这段代码时，Rails 将从缓存中获取块的结果。这可以在 Rails 应用程序中的任何地方使用，并且是缓存外部 API 和其他慢速数据获取内容的好方法。

#### 进一步阅读

[https://guides.rubyonrails.org/caching_with_rails.html](https://guides.rubyonrails.org/caching_with_rails.html)
T3】https://devcenter.heroku.com/articles/caching-strategiesT5[https://blog . app signal . com/2018/03/20/fragment-caching-in-rails . html](https://blog.appsignal.com/2018/03/20/fragment-caching-in-rails.html)

* * *

## 杂项

#### 使用快速 JSON 宝石

例如，如果你有一个 API 并使用 ActiveModelSerializers gem，那么你应该试试网飞的 [fast_jsonapi gem](https://github.com/Netflix/fast_jsonapi) 。它有更好的性能。你也可以尝试常规 JSON 工作的 [oj gem](https://github.com/ohler55/oj) 。这对于大量的 JSON 数据来说有很大的不同。

#### 使用涡轮增压器

如果您使用[刺激](https://stimulusjs.org/handbook/origin)来处理 Javascript，或者[在 Rails](https://github.com/shakacode/react_on_rails) 或类似的东西上做出反应，那么您就不必再像过去对 JQuery 意大利面条那样害怕可怕的事件多重绑定。Turbolinks 将使应用程序明显更快，我认为这是值得的。

#### 使用链接预取

如果你在用户停留在一个链接上的时候开始加载下一个页面，你可以让网站看起来更快。详见[本文](https://www.mskog.com/posts/instant-page-loads-with-turbolinks-and-prefetch/)。

#### 不用循环渲染

如果你的视图中有一个`each`-循环，比如为一个集合中的每一个条目渲染一个片段，那么这将会非常昂贵。而是像这样使用集合渲染功能:
`<%= render partial: 'item', collection: @item, as: :item %>`。这要快得多，因为在这种情况下，如果用循环呈现，Rails 将只初始化模板一次，而不是为每个项目初始化一次。

#### 对于大型数据库集合，使用 find_each 代替 find

如果你想对一大块数据进行循环，那么你不必一次把所有的数据都加载到内存中。例如，只需使用`ActiveRecord#find_each`方法代替`all`来批量加载。默认情况下，Rails 将一次加载 1000 个项目。

#### 为您的资产使用 CDN

得益于 Rails 内置的指纹识别功能，Javascript、CSS 和图像等资产可以轻松缓存。这些最好由 CDN 来提供，以获得最佳性能。你可以免费使用 [Cloudflare](https://www.cloudflare.com) 超快速加载你所有的可缓存资产。如果你想要一个不是免费的替代品，那么我推荐 [BunnyCDN](https://bunnycdn.com) 作为一个低成本的替代品。

#### 最小化 Javascript 包大小

如果你只是把世界上所有的 Javascript 都放到你的应用中，那么`application.js`文件可能会很大，如果你节省了 100 毫秒的请求时间也没什么关系，因为网站仍然会很慢。我推荐使用 [Bundlephobia](https://bundlephobia.com/) 来查找任何 npm 包的大小，然后将它添加到您的站点。

#### 一定要实时吗？没有吗？试试定时缓存！

例如，如果您的应用程序中有一个昂贵的计算或一个不需要实时的缓慢的外部 API 调用，那么您可以像这样使用定时缓存:

```
Rails.cache.fetch('some_key', expires_in: 1.hour) do
  # some expensive thing
end 
```

Enter fullscreen mode Exit fullscreen mode

那么你的应用程序将每小时只执行一次昂贵的操作。

## 高级

这些提示稍微高级一些，正确设置起来比较费时。仅供紧急情况下使用！

#### 试试 JRuby

JRuby 是在 JVM 上运行的 Ruby 的 Java 实现，对于大多数应用程序来说，它应该比 MRI 快。但是它会消耗更多的内存。关注 [Truffleruby](https://github.com/oracle/truffleruby) 获得更多奇特的性能改进。

#### 对工作使用更快的语言

如果您有大量的后台工作，而 Ruby 又不够快，那么您可以使用另一种语言来处理队列。Sidekiq 有一个[版本，使用 Crystal](https://github.com/mperham/sidekiq.cr) ，比 Ruby 快得多。还有 [Faktory](https://github.com/contribsys/faktory) 可以让你使用多种编程语言来处理工作。您还可以将[亚马逊 SQS](https://aws.amazon.com/sqs/) 与您选择的任何语言配合使用。这可以通过使用 [Shoryuken gem](https://github.com/phstc/shoryuken) 与您的应用程序集成。Faktory 和 Shoryuken 都支持 ActiveJob，所以你不需要做太多改变就可以使用它们。我个人会从 Crystal 开始，因为这是一种语法与 Ruby 相似的语言，而且速度非常快。

#### 异步局部渲染

正如我们之前讨论的，Rails 中的视图呈现会占用相当多的请求时间。如果你的站点的某些部分渲染很慢，那么你可以尝试异步加载它们。这意味着服务器将用 ajax 加载部分内容。你可以使用 [render_async gem](https://github.com/renderedtext/render_async) 来帮助你。

#### 预取链接

您可以使用 ajax 在后台加载下一个页面，这将使页面加载看起来是即时的。有几种方法可以做到这一点，比如当用户悬停在一个链接上时使用 [Instantclick](http://instantclick.io/) 加载下一页，或者当浏览器空闲时使用 [quicklink](https://github.com/GoogleChromeLabs/quicklink) 加载新页面。通过使用我在这篇文章中写的关于[的一些技巧，你也可以将 Turbolinks 和 Instantclick 结合起来。](https://www.mskog.com/posts/instant-page-loads-with-turbolinks-and-prefetch/)

#### 使用物化数据库视图

数据库视图有点像由 SQL 查询创建的虚拟表。您可以将大型查询保存为视图，然后像访问表一样访问它。物化视图类似于视图，但是它也保存查询的结果。因此，它有点像缓存的数据集。显然这非常快，但数据会很旧。然而，如果谨慎使用，它可以为表演创造奇迹。您可以使用[风景宝石](https://github.com/scenic-views/scenic)来处理您所有的数据库视图需求。不过要确保你使用的是最新版本的 PostgreSQL(>= 9.4)。

#### 放弃

我们到了。你什么都试过了，但都没用。也许是时候放弃了？或许给 Node 一个机会？Elixir 现在有点热，它的框架 Phoenix 与 Rails 非常相似，所以可能适合？或者干脆彻底放弃，用 SPA 做全 Javascript。祝你好运！