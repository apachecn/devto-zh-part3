# 如何调试 SQL 数据库

> 原文：<https://dev.to/lleonardogr/how-to-create-an-evolutive-architecure-from-legacy-databases-pt-3-32fl>

这篇文章是讨论如何为 SQL 数据库创建良好架构的系列文章的一部分:

| [如何使用 GitHub 和 Visual Studio 对 SQL 数据库架构进行版本控制](https://dev.to/lleonardogr/how-to-create-a-scalable-architecture-when-the-businessdomain-logic-is-in-the-database-2bko) |
| --- |
| [如何组织 SQL 遗留数据库](https://dev.to/lleonardogr/how-to-create-an-evolutive-architecure-from-legacy-databases-pt-2-2df9) |
| **如何调试 SQL 数据库** |
| [如何将 SQL 数据库迁移到新版本](https://dev.to/lleonardogr/how-to-migrate-sql-databases-to-a-new-version-server-58mc) |

首先，我们建立一个数据库版本控制的环境，然后我们致力于创建一个新的框架来组织我们在服务器上创建的代码，现在让我们来谈谈我们可以在数据库中使用的另一个功能，来帮助我们，**调试**。

### 在数据库中调试

这一部分对我来说很奇怪，因为我从未见过有人在数据库中调试，原因很明显:**调试模式的性能要差得多**。但是，现在调试是大多数程序员不可或缺的工具，我认为如果我没有调试，我就不能解决 90%的编程问题。微软[的一篇文章](https://docs.microsoft.com/pt-br/sql/ssms/scripting/run-the-transact-sql-debugger?view=SQL-server-2017)很好地解释了在你的 SQL 服务器上拥有这个特性的步骤。

那么，如何在不影响性能的情况下将如此重要的资源引入数据库呢？

### 策略:准备多个环境

[![0_uBWA5AQwxqqp17Gf](img/ccbc76ba35e2d54a032ca3fe2ceaf464.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IAiNVtHW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encaixandoblocoshome.files.wordpress.com/2019/04/0_ubwa5aqwxqqp17gf.png)

当我们谈论系统时，公司已经接受了拥有开发、认证和生产环境的想法。这些环境允许我们以一种更少问题的方式从新功能进行转换，即使交付有些官僚化。有了开发环境，我们可以自由地工作，安静地调试货币和工作，只是这将有助于安排新功能和维护可能的错误。
[中](https://medium.com/venture-garden-group-technology-blog/effective-development-environments-development-test-staging-pre-production-and-production-environmen-a3a85cd349b2)的一篇文章很好的解释了这个过程。

#### 生产数据与开发同步

重要的是要记住，如果我们总是想获得新功能或我们的基础可能正在经历的错误的最佳真实证明，我们需要用开发环境更新数据。这种同步可以是瞬时的，也可以是预定的，但如果可能的话，请做好准备。

#### 向我的基础架构添加另一个数据库可能会非常昂贵

如今，强大的数据库没有几个字节的信息，相反，最大的技术公司正在处理千万亿字节的数据，进行实时和大规模的传输和分析。在我们目前的情况下，在强健的基础架构中拥有磁盘空间来开发软件，这可能会非常昂贵，如果我们希望环境尽可能接近生产环境，成本甚至会更高。但是随着时间的推移，处理数据库的本地基础设施变得越来越便宜。开源基础设施、存储和数据处理解决方案越来越多地出现在世界上。特定于数据库的云计算解决方案会受到公众的欢迎，从而使您能够使用更多的资源。因此，请花些时间分析如何调整基础架构的成本，以便拥有多个环境。

这些建议，再加上本文的其他部分，你将拥有一个更健壮、更有准备的组织日常环境，更不容易失败，更有组织性。