# 在 Heroku 应用程序中运行 Heroku CLI 命令

> 原文：<https://dev.to/databasesponge/running-heroku-cli-commands-in-an-heroku-application-2j7f>

# TL；速度三角形定位法(dead reckoning)

使用 [Heroku CLI buildpack](https://github.com/heroku/heroku-buildpack-cli) 您可以针对 Heroku 应用程序执行 CLI 命令。

# 设置

添加构建包并重新部署代码。

有一些你必须要做的安全凭证的事情，但是它们被记录在[这里](https://devcenter.heroku.com/articles/authentication)。

不要忘记，您可以使用`heroku run bash -app my-application`连接到您的应用程序，运行凭证命令来获取一个 API 密钥以存储为`HEROKU_API_KEY`环境变量，并进行测试。

# 你能用它做什么

您可以从 Heroku 应用程序中运行命令行来完成通常可以从 Heroku 命令行完成的任何事情。

您可以在您的应用程序中运行这些命令，或者使用 Heroku Scheduler 插件，并且您可以使用适当的凭证从不同的应用程序中执行这些命令。

例如:

*   运行 PostgreSQL 数据库的手动备份。
*   不使用附加组件重新调整您的 dynos。
*   在每天工作开始前的特定时间重启你的 dynos。
*   使用任意大小的一次性运行 dyno 从系统内部运行 rake 任务。

我们目前正在使用它来在夜间缩减一个系统，并在工作日开始之前再次将其扩大，并在当地时间凌晨 5 点重新启动另一个系统的 dynos。

我们通过一个单独的应用程序来实现这一点，通过 Heroku Scheduler 插件发出命令。

# 从您的应用程序中运行 rake 任务

我对试验这个想法很感兴趣。

我们有一种特定类型的批处理作业，其工作负载会有很大变化:

*   小到应用程序可以在几秒钟内同步执行。
*   对于同步来说太大了，但是对于一个标准 2 倍的 worker dyno 来说在后台处理还是不错的。
*   对于标准的 2X 来说太大了，而且太费时间，不允许工人被捆绑。也许是一小时的工作。

我可以看到我们采用允许我们判断需要哪种方法的逻辑，并执行适当的逻辑。

# 总结

就是这样。