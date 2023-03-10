# SQLite 不是服务器

> 原文：<https://dev.to/lefebvre/sqlite-is-not-a-server-56il>

人们经常询问在多个应用程序之间共享 SQLite 数据库的方法。本质上，他们想知道如何使用 SQLite 作为服务器，最常见的问题是将 SQLite 数据库文件放在共享网络驱动器上，并从多台计算机上运行的应用程序访问数据库文件。这是一个非常糟糕的想法，甚至 SQLite 的人也会告诉你。我将引用他们的“何时使用 SQLite”页面中的相关部分:

> **客户机/服务器 RDBMS 可能更好工作的情况**
> 
> *客户端/服务器应用*
> 
> 如果有许多客户端程序通过网络向同一个数据库发送 SQL，那么使用客户端/服务器数据库引擎而不是 SQLite。SQLite 可以在网络文件系统上工作，但是由于大多数网络文件系统都有延迟，所以性能不会很好。此外，文件锁定逻辑在许多网络文件系统实现中存在问题(在 Unix 和 Windows 上)。如果文件锁定不能正常工作，两个或多个客户端可能会同时尝试修改同一数据库的同一部分，从而导致损坏。因为这个问题是由底层文件系统实现中的错误引起的，所以 SQLite 无法阻止它。
> 
> 一个很好的经验法则是，在通过网络从多台计算机同时直接访问同一个数据库(不需要中间的应用服务器)的情况下，避免使用 SQLite。

那么，当您想要共享您的数据库时，您有什么选择呢？

如果你想坚持使用 SQLite，那么你需要在它的前面放一些可以处理来自多个客户端应用程序的请求的东西。最明显的解决方案是通过使用带有 WebApplication 的 web 应用程序来创建 web 服务。HandleURL(或 HandleSpecialURL)。web 应用程序可以接受来自多个客户端应用程序(或任何类型—桌面、web、移动等)的请求。)，从 SQLite 数据库获取请求的数据，然后以 JSON 的形式发送回来。这是因为 web 应用程序是唯一连接到 SQLite 数据库的应用程序。

Eddie 的电子 Web 服务示例向您展示了如何进行设置:Examples/Communication/Web Services/EddiesWebService

建立自己的数据库 web 服务的另一个选择是使用 [Aloe 开源项目](https://aloe.zone/)，它为构建 web 服务提供了一个健壮的框架。

要了解更多关于数据库 web 服务的信息，请查看[制作数据库 Web 服务视频](https://youtu.be/X58zSmm2iSE)。

顺便说一下，这也是为什么一个常规的 web 应用程序可以安全地与多个用户一起使用 SQLite——web 应用程序管理多个用户，但它是唯一连接到 SQLite 数据库的应用程序。

如果您想继续使用 SQLite，但不想创建 web 服务，那么可以考虑使用一种在 SQLite 周围放置数据库服务器的产品。CubeSQL 和 [Valentina](https://www.valentina-db.com/en/sqlite-database-server) 已经有产品可以做到这一点。

您最后的选择是切换到实际的数据库服务器。PostgreSQL 和 [MySQL](https://www.mysql.com) 是流行的替代品。