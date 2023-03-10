# 消息队列 MUD 停靠更新

> 原文：<https://dev.to/matthewodle/message-queue-mud-dockerization-update-2119>

[原帖](http://blog.matthewodle.com/message-queue-mud-dockerization-update/)

我在做的那块泥巴上取得了进展。

尝试一下(是的，给事物命名很难):

```
git clone -b v0.1.0_share https://gitlab.com/modle13/queue-mud.git
cd queue-mud
docker-compose up 
```

周末的时候，我让它几乎恢复到对接之前的状态。逻辑有点少(生物决策点只返回一个字符串)，角度前端仍然是禁用的。在一月初，我有一个发布者和消费者，但现在它有整个链:命令>验证>攻击，如果命令是攻击，并且有一个目标>响应队列。

我解决的主要问题是如何为不同的决策点建立单独的队列。我还解决了让 gunicorn 和 python 将 stdout 和 stderr 正确输出到 docker-compose 日志的问题(查看 Dockerfile CMD 标志；`-u`用于 python，`"--access-logfile", "-", "--error-logfile", "-"`用于 gunicorn)。我学习了一些关于命名服务和容器的知识，查询 RabbitMQ API，`.env`docker-compose 文件，以及在多个容器中使用定制 python 模块。

在添加了第二个和第三个队列之后，我有强烈的冲动将样板文件抽象成一个模块(这就是模块的用途:可扩展样板文件:D)，所以我这么做了，而且非常棒。我还添加了一个发布模块，并将 publisher flask 应用程序的职责划分为一个 ingester(仅限 flask `POST`和`status`端点)和一个 publisher 模块(简单的 python，用于所有决策点和 ingester)。这样我就完全不用担心样板文件了。我只是在决策点初始化时设置了`queue`和`exchange`参数以及自定义的`on_message`函数来建立一个新队列，并将目标交换机的名称和消息一起传递给发布者模块。

今天的重点是清理它，这样用起来就不会那么难受了。编写共享模块是一个很大的进步。现在新的模块只需要设置两个参数并覆盖`on_message`方法。我可能应该用继承来做这件事，但是现在这样就可以了。我清理了`README`，列出了必需的和可选的系统依赖项(只有`docker-ce`和`docker-compose`是必需的)。我添加了一个 tmuxinator 配置文件(太酷了)，它启动了定制的`queue_watcher`脚本(查询 RabbitMQ API 并使用 python 解析出一些数据)、log tailer(响应队列消费者输出的消息)、docker-compose(实际运行游戏的东西)和一个验证步骤(`verify.sh`，它将一些示例值传递给 ingester 并检查`status`端点)。

状态端点还有一个不必要的功能。它使用 goodreads API 随机提取乔治·奥威尔的名言。[这是模块](https://gitlab.com/modle13/goodreads-quotes/blob/f271f696a67b3232a2f9daa9f264ba89fefb8fdb/get_quote.py)。不要拿`try` / `except`开玩笑。

接下来的步骤是弄清楚如何处理世界状态，以及如何管理生物，两者都可能有相同的问题..目前，响应只是转储到卷装载的日志文件中。目前这很好(它可以在本地播放)，但最终将需要考虑大小。能够回放历史也是很有趣的，为此需要一些更聪明的东西。