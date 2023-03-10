# 生活、记录和繁荣

> 原文：<https://dev.to/molly/live-log-and-prosper--3j65>

我想写这篇文章，因为当我发出一个优化请求时，最常见的问题是，“你到底是怎么发现的？!"我 90%的回答是，“我读日志。”

我认为读日志是一门失传的艺术。我们有如此多令人敬畏的监视工具，允许我们监视我们的应用程序，以至于我们很容易忘记像日志这样简单的资源。监控工具对于全面了解您的应用程序非常有用。然而，当涉及到您的代码如何与您的数据存储和第三方服务交互时，没有什么比阅读日志更好的了。

## 优化代码

下次你想做一些代码优化的时候，可以考虑这样做。进入您的开发或试运行环境，将您的日志设置为随处调试！

为您的框架设置它。

```
pry(main)>  Rails.logger.level = 0 
```

Enter fullscreen mode Exit fullscreen mode

为你的宝石而设。

```
pry(main)>  Search.connection.transport.logger = Logger.new(STDOUT) 
```

Enter fullscreen mode Exit fullscreen mode

为你的每一个相关服务设置它。

```
$  redis-cli monitor > commands-redis-2018-10-01.txt 
```

Enter fullscreen mode Exit fullscreen mode

一旦设置了日志配置，就可以放开应用程序了。加载网页，运行后台工作人员，甚至跳转到控制台，手动运行一些命令。完成后，查看生成的日志。这些日志将告诉您许多关于您的代码如何与您的所有数据存储和第三方服务交互的信息。我敢打赌，其中的一些，并不像你所期望的那样相互作用。在应用程序中进行优化时，像读取日志这样简单的事情是多么有价值，我怎么强调都不为过。

## 将日志融入您的工作流程

有时候找到一段代码进行优化**就像**阅读日志一样简单。其他时候，可能会更复杂一点。这是我的团队如何使用日志来查找和修复一段代码的一个例子。

出现问题的第一个迹象是我们从监控服务得到的 Redis 高流量警报。

[![](img/14b11af577a79bd4d5f21a05dbacc0e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ynNV6v5J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/azs8i15r4kmk2clx0xp1.png)

通常，当一段代码在我们的应用程序中表现不佳时，我们首先看到的是某种警报，如高流量警报、慢速查询警报、超时警报等。收到此警报后，我们做的下一件事是查看警报发生时的 Redis 流量图表。

[![](img/435d6636426ba61454296fdd0efa10df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9jJB_AhG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0d0ywnm1t9r0r7dz3rym.png)

从这个图中我们可以看到，有问题的代码在早上运行了几个小时。既然我们知道了时间和地点，下一步就是要弄清楚是什么。这就是服务监控派上用场的地方。使用您的监控工具找出当时正在运行的程序。使用我们的监控服务，我们能够计算出在那个时间窗口中，我们运行了许多特定类型的 Resque 工作线程。

一旦我们确定了那个工人的流量，最后一个难题就是弄清楚那个工人做了什么导致了负载。这就是读取日志的用武之地！

因为我们知道是什么工作箱导致了这个问题，所以我们跳到了其中一个工作箱上，启动了 redis-cli。

```
$  redis-cli monitor 
```

Enter fullscreen mode Exit fullscreen mode

我们让它运行 30 秒，而工人们正在工作，这是我们得到的一个片段。

```
# REDIS LOG FORMAT
# timestamp [redis_db ip_address:PID] command argument

1544451182.095873 [15 1.1.1.1:15288] "exists" "worker-01-shard-1"
1544451182.095888 [15 1.1.1.6:63462] "exists" "worker-02-shard-3"
1544451182.095941 [15 1.1.1.4:55676] "exists" "worker-05-shard-3"
1544451182.095952 [15 1.1.1.8:55596] "exists" "worker-02-shard-3"
1544451182.095983 [15 1.1.1.8:55682] "exists" "worker-09-shard-2"
1544451182.096225 [0 1.1.1.11:19286] "llen" "batching:indexing-worker:medium_priority"
1544451182.096260 [15 1.1.1.4:55564] "exists" "worker-03-shard-3"
1544451182.096280 [15 1.1.1.7:18378] "exists" "worker-02-shard-3"
1544451182.096309 [15 1.1.1.4:55594] "exists" "worker-02-shard-2"
1544451182.096439 [15 1.1.1.5:14774] "exists" "worker-03-shard-2"
1544451182.096502 [15 1.1.1.7:12098] "exists" "worker-03-shard-3"
1544451182.096672 [15 1.1.1.1:32618] "exists" "worker-03-shard-3" 
```

Enter fullscreen mode Exit fullscreen mode

令我们惊讶的是，我们发现我们从一组特定的 worker boxes 发出了大量的 EXISTS 请求。然后，我们将 EXISTS 请求追溯到一个 gem，我们用它来限制我们的 Resque 工作程序。我们很确定是这些请求压倒了 Redis。剩下要做的最后一件事就是确认它。我们删除了节流代码，果然，这消除了 Redis 流量。

[![](img/fec4d450b0e4ce15a697a59ea1e708ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uaLZUrrV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ivg8h4qr64rn4hrztlu.png)

## 大图

我展示的代码示例使用了 Ruby，但是老实说，读取日志的概念可以应用于任何语言。下一次当你发现自己面对缓慢的代码或找不到的 bug 时，考虑阅读你的日志。最好的情况是，他们帮助你解决你的问题。最坏的情况是，你对你的代码是如何工作的有了更好的理解。

编码快乐！

[![](img/c70d0ba0fb350f44e6ca79b0a6eecc4a.png)](https://i.giphy.com/media/IL4iTvQH0MjS/giphy.gif)