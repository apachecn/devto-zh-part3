# 星期五爆炸#56

> 原文：<https://dev.to/horia141/friday-blast-56-438e>

[16 种测量网络效应的方法(2018)](https://a16z.com/2018/12/13/16-metrics-network-effects/)——某公司有*网络效应*当用户从新用户进入平台中获益时。把脸书、推特或优步想象成科技领域这类企业的典型例子。这篇文章列出了不少于 16 个指标来描述这样的公司。显然对*内部人士*有用，尽管其中一些也由上市公司报道。

UNIX 可以自动完成的事情(2010 年) -我最近需要从 Java 进行一些进程间同步，并考虑使用文件系统方法进行锁定。这变成了对“我实际上可以锁定的东西”的盲目追逐，结果证明仅仅链接和文件/目录创建(在某些模式下)是可以的。

[锁定 Java 中文件的存在(2014)](https://stackoverflow.com/questions/26850724/lock-on-existence-of-file-in-java)——一个 SO 线程，我在那里开始了我的搜索，并导致了前一篇文章。我最后用的就是从这里捡的，也就是`File.createFile`。

两个标准的故事(2005 年)-POSIX 和 Win32 文件 API 的比较。作为程序的目标，我有点错过了 Windows 上的那艘船，所以我对它的内容只有最模糊的了解。除了 ASP.NET 的工作，这是远离任何系统层次。因此，我总是对构建该系统的人的技术能力印象深刻，并且对业务方面在坚实的技术部分进行了有些不必要的抨击。此时仍在阅读锁定。

[关于文件锁定破坏的附录](http://0pointer.de/blog/projects/locking2) -文件锁定的更多灾难。但是当着 NFS 的面。我们*都知道*从一开始就有问题，因为它不能很好地提供 POSIX 兼容性。OTOH，在一些云提供商那里，你永远不知道你正在写入的磁盘是本地的还是远程的。所以“这不会发生在我身上”不是尝试 POSIX 中文件锁定的有效借口。