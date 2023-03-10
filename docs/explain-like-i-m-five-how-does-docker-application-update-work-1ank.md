# 像我五岁一样解释:docker 应用程序更新是如何工作的？

> 原文：<https://dev.to/marcel_cremer/explain-like-i-m-five-how-does-docker-application-update-work-1ank>

每当我读到 docker 或容器时，我总想知道它在现实应用中是如何工作的。

这是我(认为我)直到现在才明白的:

*   docker 封装了一个应用程序，但不是应用程序数据
*   通过完全交换应用程序容器来完成更新(例如，移除 myApp 1.0.0 并启动 myApp 1.0.1)
*   所有非易失性数据都存储在容器外部的卷中(基本上是主机和 docker 容器之间的共享目录)

然而，如果是这样做的话，更新过程/数据库模式如何更新/...工作？

一个更具体的例子:如果我有一个 postgres 容器，我需要更新数据库版本(因此:安装新的容器)。当 postgresql 安装/更新程序永远不会执行时，如何将数据库文件更新到较新的版本？