# 调试 Laravel 队列睡眠器

> 原文：<https://dev.to/glennmen/debugging-laravel-queue-sleeper-21hn>

上周，我花了 3 个小时调试我的 Laravel 队列工作器的一个问题。

这是我第一次与 Laravel Queue、Redis 和 Supervisor 一起工作，所以我预料会出问题。当我运行`php artisan queue:work`时，它开箱就能正常工作，没有抛出任何错误。它连接到我的 Redis 服务器，我的工作是连接到 AWS S3 清理文件。

在这一点上，我非常高兴，一切都像它应该的那样工作。然后，按照文档的建议，我开始设置 Supervisor，以确保 worker 进程总是在运行。我根据建议的示例进行了配置:

```
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /home/forge/app.com/artisan queue:work --sleep=60
autostart=true
autorestart=true
numprocs=2
redirect_stderr=true
stdout_logfile=/var/www/.../storage/logs/worker.log 
```

当然，我添加了一些变化。因为这是一项新服务，还没有很多作业，所以我将工作进程减少到只创建 2 个，并且如果没有作业可用，我会添加 60 秒的睡眠。

当我重启监控器来监控我的工作进程时，我看到了一些奇怪的行为，它正好在一分钟后重启我的进程。你们中的一些人现在可能已经明白为什么会发生这种情况，但我当时不在。

所以我开始调试，奇怪的行为只有在我添加了管理员后才开始。是的，它正在重新启动工作进程，并且正在处理作业，但是这不是正确的行为。我开始浏览配置，查看 Laravel 文档，查看主管文档，stackoverflow 和 Laracasts。你知道任何人都会去的地方。

我认为`user`可能需要如此尝试，还是一样。我试着添加了`directory`，因为我在某处读到过，可能 Laravel 不能读`.env`，也没有区别。我尝试了很多不同的东西，因为我确信这是我的主管配置中的东西。

有一段时间，我甚至认为 Redis 或 AWS S3 在工作中出现了一些错误，但日志中什么也没有。

我不知道为什么，因为我没有在网上找到它，但是我把`--sleep=60`简化为`--sleep=30`,的确，在那之后，工作进程继续运行，并且仍然在运行。似乎默认情况下(您可以覆盖该值),如果 Laravel 队列 worker 在 60 秒内没有做任何事情来删除任何“冻结”的子队列进程，它就会停止一个进程。

最后，这是我的主管配置:

```
[program:laravel-worker]
process_name=%(program_name)s
command=php /home/forge/app.com/artisan queue:work --sleep=30
autostart=true
autorestart=true
numprocs=1
redirect_stderr=true
stdout_logfile=/var/www/.../storage/logs/worker.log 
```

**我对 Laravel Docs 做了一个 PR 来添加一个注释，声明`--sleep`应该比超时值短。这将节省我 3 个小时。**

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 添加了警告信息 #5120](https://github.com/laravel/docs/pull/5120) 

[![Glennmen avatar](img/b208733bf7be977cfd1b09cfcfd1f625.png)](https://github.com/Glennmen) **[Glennmen](https://github.com/Glennmen)** posted on [<time datetime="2019-04-10T11:37:03Z">Apr 10, 2019</time>](https://github.com/laravel/docs/pull/5120)

我的主管配置中有`--sleep=60`,这导致了一个问题，1 分钟后我的工人停止了工作。我不知道`--timeout`,也不知道它没有看到一个作为活动工作者的“休眠”进程。我希望通过添加这个小警告，我可以节省其他人调试这个问题的时间。还发了一篇关于这个问题的博文: [DEV.to](https://dev.to/glennmen/debugging-laravel-queue-sleeper-21hn)

我也不确定目标分支应该是什么，因为旧版本也可以从这个警告消息中受益。当我遇到这个问题时，我正在进行一个 Laravel 5.7 项目。

[View on GitHub](https://github.com/laravel/docs/pull/5120)

我没有疯，这是任何程序员都会遇到的事情。我希望我能帮助避免人们有同样的问题。