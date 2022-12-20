# 我在我的系列文章“几周内的几批数据库”中介绍了 PostgreSQL Dev 的设置和安装

> 原文：<https://dev.to/adron/i-cover-postgresql-dev-setup-and-installation-in-my-series-bunches-of-databases-in-bunches-of-weeks-5ap3>

愿数据库洪流开始，是时候“在一堆一堆的星期里建立一堆数据库”了。我们将开始查看数据库，类似于在“ [7 周内 7 个数据库](https://pragprog.com/book/rwdata/seven-databases-in-seven-weeks)”中处理它们的方式。在这个环节中，我仔细研究了一下 [PostgreSQL](https://www.postgresql.org/) ，或者就像有些人说的那样，只是 Postgres。这是我在 Ubuntu 上本地安装数据库的几个 PostgreSQL 会话中的第一个。它可以移植到任何其他操作系统上，PostgreSQL 就是这么棒。然后在安装并获得 pgAdmin 4(PostgreSQL 的用户界面)后，我选择了 Docker 路线。同样，指向 pgAdmin 4 并创建一个数据库和一个初始表。

在视频下面，我添加了时间线和其他细节，链接，以及这个系列的其他相关信息。

[https://www.youtube.com/embed/UD5cQ9fMYcQ](https://www.youtube.com/embed/UD5cQ9fMYcQ)

[0:00](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=0s) -介绍图像拼接和带有曲调的金属介绍..
[3:34](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=214s)——视频数据库内容的开始。
[4:34](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=274s) -开始在本地机器上本地安装 Postgres/PostgreSQL。
[20:30](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=1230s) -在本地机器上安装 pgAdmin 4。

[1:00:05](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=3605s)——安装 Docker 并获取 PostgreSQL 设置为容器！
[1:00:36](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=3636s) -在数字海洋的博客中添加了 [stellar 帖子的链接。](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04) [1:00:55](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=3655s)——我声明，如果数字海洋只是提供文档，我很乐意为其付费，他们的博客条目、教程和文档无疑是网络上最好的！
[1:01:10](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=3670s)——在 Ubuntu 18.04 上安装 Postgesql。
[1:06:44](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=4004s)——登录 Docker hub，找到官方 Postgresql Docker 镜像。
[1:09:28](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=4168s) -用码头工人启动集装箱。
[1:10:24](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=4224s)——用 pgadmin4 连接到 Docker Postgresql 容器。
[1:13:00](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=4380s)——针对 Docker 容器，使用 pgAdmin4 创建数据库并使用 SQL、表和其他资源。
[1:16:03](https://youtu.be/UD5cQ9fMYcQ/watch?v=UD5cQ9fMYcQ&t=4563s)——黑客越狱 outtro。快乐的代码！

对于“[周群](https://www.youtube.com/playlist?list=PL4f75Y6Ce5ssKPOFLtPjUmDWsinO4K-c4)中的数据库群”系列的每个会话，我将遵循以下顺序。我将浏览[中的每个数据库，这是我第一天的前 7 个数据库列表](http://compositecode.blog/2019/01/04/lets-talk-top-7-options-for-database-gumbo/)(见下文)，然后将浏览每个数据库，工作到第二天，以此类推。类似于“ [7 数据库在 7 周内](https://pragprog.com/book/rwdata/seven-databases-in-seven-weeks)累积额外的天数

数据库的第 1 天，我将致力于构建特定数据库的开发安装。例如，在这个会话中，我通过将 PostgreSQL 安装到本地机器来设置它，并且还提取了一个 Docker 映像来运行 PostgreSQL。

“**第 2 天**”各自的数据库，我将开始使用 CQL、SQL 或任何可以直接使用数据库的工具来处理数据库。在这一点上，我还将更深入地研究在各自的数据库中插入和存储数据的类型。

" **Day 3** "各自的数据库，我将开始用 C#、Node.js 连接一个应用程序，然后开始。实现一个简单的连接，对连接进行预期的测试，并对在同一数据库的前 2 天建立的相应数据库进行简单的插入、更新和删除。

"**第 4 天**"以后我会决定话题的路径和布局，所以在 [YouTube](https://www.youtube.com/channel/UCHSCXYCMOOS-VSUdRsn13GQ) 和 [Twitch](https://www.twitch.tv/adronhall/) 上订阅，然后收听。[事件被安排在](https://www.twitch.tv/adronhall/events)，当你想在 Twitch 上观看[的特定剧集即将播出时，你可以选择得到通知。](https://www.twitch.tv/adronhall/events)

### "*天内的数据库群*的下一个事件

*   [数周内的大量数据库- SQL Server 第 1 天](https://www.twitch.tv/events/5JIXYj8rR-yp8Jv_Uy1LfQ)
*   [数周内的数批数据库-弹性搜索第 1 天](https://www.twitch.tv/events/Daf4_TYORvqxIbUFpc-TNw)
*   [成束的数据库成束的星期- Redis 第 1 天](https://www.twitch.tv/events/7BCRmc6zT0yXMYIBcHvQig)
*   [成束的数据库成束的星期- SQLite 第 1 天](https://www.twitch.tv/events/oaDIP0yEQAmIKWhAxoO0pg)
*   [成束的数据库成束的星期- DynamoDB 第 1 天](https://www.twitch.tv/events/D0jI84RmS9-x0XdpW-eUpg)

我在 Twitter [@Adron](https://twitter.com/Adron) 和 Twitch [@adronhall](https://twitch.tv/adronhall/) 上一直听金属和编码，写关于编码、学习和教授各种主题，从数据库技术到编码印章。感谢阅读！