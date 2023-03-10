# 在 Heroku 和 Laravel 一起伐木

> 原文：<https://dev.to/autoidle/logging-with-laravel-on-heroku-1imf>

#### Heroku 上的 Laravel 提示 5

<figure>[![](img/4f1c565c557b861f4a974e0cd52687b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YDpmsBdl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Av3fvMLA9GJAIDRZm) 

<figcaption>照片由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

默认情况下，Laravel 会将错误和消息记录到磁盘上的一个目录中，这并不理想，因为 Heroku 使用了一个[临时文件系统](https://devcenter.heroku.com/articles/dynos#ephemeral-filesystem)，这意味着在 dyno 运行期间对文件系统的任何更改只会持续到 dyno 关闭或重启。每个 dyno 引导时都带有最近一次部署的文件系统的干净副本。这类似于 Docker 等基于容器的系统的运行数量。

更改 Heroku 上的日志目标:

```
heroku config:set LOG\_CHANNEL=errorlog 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以跟踪日志了:

```
heroku logs -t 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。🚀

### 想要更多这样的提示？

你应该在推特上关注我！如果你正在使用 Heroku，你应该看看[auto idle](https://autoidle.com)——这是一种自动化的方式，可以节省你升级和审核应用的费用。