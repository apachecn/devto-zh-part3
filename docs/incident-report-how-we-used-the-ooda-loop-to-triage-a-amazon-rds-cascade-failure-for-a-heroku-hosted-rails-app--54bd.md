# 事故报告！我们如何使用 OODA 循环对 Heroku 托管的 Rails 应用程序的 Amazon RDS 级联故障进行分类

> 原文：<https://dev.to/cohere/incident-report-how-we-used-the-ooda-loop-to-triage-a-amazon-rds-cascade-failure-for-a-heroku-hosted-rails-app--54bd>

*本文最初发布在 [Cohere 的网站](https://www.wecohere.com%5D)上，标题为[真实世界代码事件报告！我们如何使用 OODA 循环对 Heroku 托管的 Rails 应用](https://www.wecohere.com/articles/how-we-used-the-ooda-loop-to-triage-a-cascade-failure-for-a-heroku-hosted-aws-rds-backed-ruby-on-rails-app.html)T5 的亚马逊 RDS 级联故障进行分类*

圣诞节后的第二天，Notabli 联系了我们，因为他们的 API 正在下降。他们友好的工程师[泰·劳伯](https://www.linkedin.com/in/ty-rauber-69822b5/)正和他们的产品负责人[杰克森·拉特卡](https://www.linkedin.com/in/jacksonlatka)一起努力工作，让它重新运转起来。他们想要第二双程序员的眼睛，我觉得很无聊。这不就是每个人利用假期的目的吗？为了好玩而对生产问题进行分类？

### 三级串联故障——OODA 循环 1——也许是第 N+1 次查询？

我们和他们的产品负责人[杰克森·拉特卡](https://www.linkedin.com/in/jacksonlatka/)一起跳进了一个 [Zoom](https://zoom.us/) 房间，开始了调试过程。调试时，我尽量有意识地遵循 [OODA 循环](https://en.wikipedia.org/wiki/OODA_loop):观察、定位、决定、行动。

首先，*观察！* Jackson 授予我管理他们的[亚马逊关系数据库服务(RDS)](https://aws.amazon.com/rds/) 、 [Heroku](https://www.heroku.com/) 托管、 [Papertrail](https://papertrailapp.com/) 日志记录和 [Scout](https://scoutapp.com/) 应用程序监控的权利。非常感谢杰克逊对我们的信任，这是这一切如此成功的主要原因。毕竟，团队以信任的速度前进！

二、*东方！*我开始和 Ty 一起探索建筑。Notabli 的 API 是一个托管在 Heroku 上的 [Ruby on Rails](https://rubyonrails.org/) 应用程序。它使用 [Sidekiq](https://sidekiq.org/) 来执行后台工作，其中许多工作在处理视频和图像时都是计算密集型的。此外，主端点是一个由*个矩*组成的流，它由许多数据获取操作来解决，其中之一是一个 [N + 1 查询](https://secure.phabricator.com/book/phabcontrib/article/n_plus_one/)，另一个是一个大规模跨表内部连接。

第三，*决定！作为一名程序员，我立即决定尝试通过代码来解决这个问题。*

第四，*行动！*因为他们使用了 [jsonapi-resources](https://github.com/cerebris/jsonapi-resources) gem，一个我个人不熟悉的库，很难找到一个 seam 来开始解决查询问题。泰和我跌跌撞撞地穿过，戳和刺激；但 15 分钟后，我们认为这是一个死胡同。

经过反思，我意识到我违反了我的有效停机恢复原则之一:*在进行需要重新部署应用程序的更改之前，尝试首先在基础架构级别解决停机问题。*我并不严格遵循这一点，但它在过去几十年里对我很有帮助。

### OODA Loop 2——也许是磁盘？

我们回到*观察*AWS 仪表盘，我发现了什么？第一次重大停机发生在系统存储空间不足的时候！

[![The RDS Instance's Free storage space's Precipitous Decline](img/07e65f6f2dca551a4b860bfd60a5259c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jmDI_DCF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u23uswmqu4ckzn3s52ep.png)

在因为没有早点注意到这一点而短暂自责之后；我深入研究了一下，发现就在故障发生之前，服务器正以它所能提供的最大 IOPS 速度运行！对于那些看到维基百科文章时目光呆滞的人来说，IOPS 是读写磁盘操作的吞吐量。

深入研究后，我们意识到是*写操作*耗尽了所有的 IOPS。我又开始责备自己浪费了大约 30 分钟来缓解一个与 *read* 相关的问题；并提出了两步创可贴:

1.  我们升级磁盘，因为 [AWS 根据磁盘大小](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)授予 IOPS；这将使应用程序恢复运行，并允许它处理增加的流量。
2.  一旦系统稳定下来，我们就会寻找为什么会有这么多写入，并确定是否有任何写入可以从系统中删除。

谢天谢地，这奏效了。应用程序又出现了！它保持了合理的性能，并且在数据库再次发生故障时，有一种可重现的方法使应用程序恢复运行。

不幸的是，因为他们所有的工作都是在志愿者的基础上完成的；我们没有时间执行第 2 步了！

为了帮助我们花时间修复它，我们向我们的朋友和粉丝提供机会，在 2019 年 1 月 15 日美国东部时间上午 10 点/下午 1 点免费加入我们*或*，参加两个小时的 *[真实世界代码直播:防止 Heroku 托管的、亚马逊 RDS 支持的 Ruby on Rails 应用程序](https://www.wecohere.com/events/january-15th-2019-10am-pt-1pm-et-real-world-code-livestream-preventing-cascade-failures-caused-by-excessive-concurrent-writes-in-a-heroku-hosted-amazon-rds-backed-ruby-on-rails-app/?utm_campaign=real-world-code-2019-01-17)* 中过多的并发写入导致的级联故障。我们不确定是否会完全诊断和调试为什么写级联会在单个会话中发生，因此我们可能会在 1 月 29 日同时进行第二个会话！**