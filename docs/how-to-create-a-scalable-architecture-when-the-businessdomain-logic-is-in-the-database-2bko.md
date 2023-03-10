# 如何使用 GitHub 和 Visual Studio 对 SQL 数据库架构进行版本控制

> 原文：<https://dev.to/lleonardogr/how-to-create-a-scalable-architecture-when-the-businessdomain-logic-is-in-the-database-2bko>

这篇文章是讨论如何为 SQL 数据库创建一个好的架构的系列文章的一部分

| **如何使用 GitHub 和 Visual Studio 对 SQL 数据库架构进行版本控制** |
| --- |
| [如何组织 SQL 遗留数据库](https://dev.to/lleonardogr/how-to-create-an-evolutive-architecure-from-legacy-databases-pt-2-2df9) |
| [如何调试 SQL 数据库](https://dev.to/lleonardogr/how-to-create-an-evolutive-architecure-from-legacy-databases-pt-3-32fl) |
| [如何将 SQL 数据库迁移到新版本](https://dev.to/lleonardogr/how-to-migrate-sql-databases-to-a-new-version-server-58mc) |

在公司和老的 T.I 专业人员中有一个非常常见的问题，即**业务逻辑需要在数据库**中，尽管这个讨论仍然存在争议，有些人说必须考虑其中的好处。

事实是，多年来，这种方法的许多缺点被发现，并被替换为更健壮的解决方案。

### 在数据库中遗留业务逻辑的常见问题

事实上，我工作过的大部分公司都使用这种方法，起初这让我很恼火，毕竟，我用这种方法遇到了几个问题，比如:

*   代码版本控制几乎是不存在的，除非你有一些以前版本的注释代码
*   调试是不可能完成的，因为数据库不是为您调试应用程序而设置的，并且通常需要在本地机器上复制大量的“调试器”程序和作业
*   异步编程、库和包可能会帮助我们成为项目中不可行的技术，因为我们不能简单地将这些包安装在银行中。由于我们不能使用类，面向对象、Solid、DDD、微服务和云计算的优势变得不可或缺，因此查询中的 SQL 代码重复非常常见

这些是我们在 SQL 中使用业务逻辑时遇到的一些问题，所有这些都是为了在“性能”方面获得一点点好处，如果应用程序不响应创建的查询，这有时可能是无效的。

### 一个难以忽视的真相

事实是，我们很少能逃脱这种模式，有太多的人受到这种方法的制约，我们很难理解如何解决这些问题，因为我们经常需要改变最大层的思维模式，使 T.I 的面积实际上有显著的变化。

我们所剩下的，至少在我们必须处理它的工作中遭受更少的痛苦，是创建一个能够使数据库健康发展的结构，并且可以容易地被替换，如何创建一个更好的结构是我们现在将在这里组装的。

#### 1-版本控制

我看到的主要问题是如何为创建的**过程**和**作业**创建一个版本控制框架。我使用的一个简单策略是找到更适合编程的软件。事实上，当我使用 C#时，我们将使用微软环境作为例子。

你需要以下物品:

*   Visual Studio(我用的是 2017 社区)
*   与 management Studio 一起安装的 SqlServer(我使用的是 SQL Server Express)。
*   GitHub 账户

#### SQL Management Studio vs . Visual Studio

SQL Management Studio 是一款出色的工具，如果您想在数据库上使用 SQL 命令来运行查询和命令，这对于 DBMS 来说很好，如果您需要纠正、提取或估算数据，但当我们谈论在我们的数据库中编程时，我们有更好的选择，请按照下面的步骤来版本化您的 SQL 数据库。 这将与微软 GitHub 上的 AdventureWorks 数据库在这个[链接](//)上一起工作。如果你想要一个关于如何在 Visual Studio 中创建数据库项目的更详细的教程，我使用了这个。

*   首先在 SQL server 上安装 AdventureWorks OLTP 数据库
*   在 Visual Studio 中创建新的数据库项目

[![NewDatabaseProject. png](img/0fea29c192968390d7c51aa165a6550e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TKJo0iRc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encaixandoblocoshome.files.wordpress.com/2019/03/newdatabaseproject.png)

*   现在，通过右键单击项目> >导入> >数据库，将您安装的数据库与项目链接起来。

[![ImportDatabase. png](img/23d62672f592ac3e95f5266c050488de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iPZeSpBW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encaixandoblocoshome.files.wordpress.com/2019/03/importdatabase.png)

*   现在，文件夹已经从基础创建

[![Folderscreated](img/a7481a5cf18c714762255bd60c5fb443.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YV8sZw36--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encaixandoblocoshome.files.wordpress.com/2019/03/pastascriadas.png)

*   最后，只需将代码添加到版本控制中

[![Addingthe Font](img/f23c98e6f7b7a05503213256e9ca355b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ip7BK7mo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://encaixandoblocoshome.files.wordpress.com/2019/03/adicionandoaocodigofonte.png)

<center>Add the database in source code control</center>

Now only give **Commit** of this and ready your project will be on GitHub with versioning control.

毕竟，您的项目是有版本的，当您更改时会有历史记录，现在您的项目将有版本，数据库管理员或程序员将可以访问项目，并可以在项目中实现改进。接下来，我们将看到**如何创建一个好的**数据库结构，以更好地分离领域并更好地理解基础。