# 略微粗糙的交换:使用 TransactionScope 解决事务性问题

> 原文：<https://dev.to/rionmonster/slightly-rough-exchanges-troubleshooting-transactional-issues-with-transactionscope-2494>

*这篇文章最初发表在[我的博客](http://rion.io)上。*

在[我的上一篇文章](https://dev.to/rionmonster/smooth-transactions-with-transactionscope-4i9o)中，我讨论了在 C#中使用`TransactionScope`，以及它如何简化应用程序中的事务管理。它涵盖了什么是事务，传统上管理事务的一些方法，以及如何使用`TransactionScope`来清理您的代码，让“魔术”来处理剩下的部分。但是众所周知，即使是最好的魔术师也会偶尔出错。

这篇文章讨论了在使用这个特性时可能遇到的一些常见问题和警告，如何克服它们，以及什么时候您可能不得不硬着头皮完全避免它。

## 一些常见的警告/问题

我将在下面详细阐述这些，但是使用`TransactionScope`时需要注意的几件事包括:

*   **处理跨不同数据库的多个连接** -这将涉及微软分布式事务协调器服务，这可能会使事情变得复杂/变得不可能。
*   **处理异步(例如 async/await)** -由于底层环境事务是静态的，多线程环境会使事情变得复杂。
*   **与超时相关的错误**——如果你开始遇到错误，确保你设置了适当的超时可能是一个挑战，默认情况下就是这样...奇怪。

## 玩家 MSDTC 已进入游戏。

[![oh-snap-msdtc](img/4c6977c06eca23d97c6fa9aee3327c67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SdMlu0lC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://www.rion.io/conteimg/2019/05/oh-snap-msdt.gif)

在同一个服务器和/或数据库的连接之间共享事务是非常琐碎的，但是**如果您开始在一个事务中查询多个不同的源/数据库，您将很快被介绍给一个名为[的新朋友微软分布式事务协调器](https://blogs.msdn.microsoft.com/florinlazar/2004/03/04/what-is-msdtc-and-why-do-i-need-to-care-about-it/)服务**，它简单地做以下事情:

> MSDTC 是为分布式系统提供事务基础结构的 Windows 服务。在这种情况下，事务意味着在分布式系统中构造自治代理之间交互的一般方式。每个事务都是一个具有四个关键属性的状态转换 ACID 属性:原子的(要么全部要么什么都没有)、一致的(合法的)、隔离的(独立于并发事务)和持久的(一旦发生，就不能废除)。有不同的技术可以实现 ACID 属性，但是最著名的技术是两阶段提交。

**一旦涉及到多个数据库，包含在`TransactionScope`中的事务就会自动升级为“分布式事务”,因此任何服务器都必须启用 MSDTC 服务才能按预期运行。**这一点非常重要，因为如果服务没有启用，调用就会失败(并且您不会总是得到世界上最好的错误消息来解决这个问题)。

如果您能完全控制您的环境，这可能不是问题。但是，如果您的产品部署在本地，或者如果您的查询依赖于 [`OPENQUERY`](https://docs.microsoft.com/en-us/sql/t-sql/functions/openquery-transact-sql?view=sql-server-2017) 或目标链接服务器，而这些服务器可能不属于您，则您可能无法保证该服务已启用。

## 处理异步

[![late-doggo](img/681d5e3f07806111236c4569cab49658.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--33MvHSBf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://www.rion.io/conteimg/2019/05/delayed.gif)

在 SPAs、承诺和并行的世界中，应用程序处理异步的情况比过去要普遍得多。像`async/await`这样的东西的出现使得这个过程变得更加容易，但是如果你正在处理事务，有相当多的事情你应该知道。

**简而言之，async/await 在现成的**transaction scope 块中不能很好地工作，需要一些额外的配置来告诉它如何通过 [`TransactionScopeAsyncFlowOption`](https://docs.microsoft.com/en-us/dotnet/api/system.transactions.transactionscopeasyncflowoption?view=netframework-4.8) 来解析操作，可能看起来像这样:

```
using (var transaction = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
     // Do your async/await work here
} 
```

另一个重要的考虑因素是**前一篇文章中讨论的支持事务的环境事务是线程静态的，因此在多线程环境中不会像预期的那样工作**。一如既往——使用您的最佳判断来确定将一系列事务组合在一起，以及是否有更适合您的环境。

## 超时！

[![lets-take-a-timeout](img/c602ed3943426015d7e6ff2379b912e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--swK80J2L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://www.rion.io/conteimg/2019/05/timeout.gif)

在处理事务时，管理超时是至关重要的，因为如果设置过高或过低，都会出现一系列问题。**过长的超时可能会导致阻塞/死锁问题，这是一个难以解决的问题，而那些因*太短*而犯错误的人可能无法完成工作。**

如果下面的错误消息看起来很熟悉，那么您可能需要重新评估现有的超时:

> 与当前连接关联的事务已完成，但尚未释放。必须先释放事务，然后才能使用连接来执行 SQL 语句。

根据你的情况，你可能会用这样的话来结束一天的工作

```
// I don't care how long it runs, just fix all my transaction issues by never timing-out
using (var transaction = new TransactionScope(TransactionScopeOption.Required, TimeSpan.MaxValue)
{
      // All your transacted operations are belong to us
} 
```

**绝对不要这样。**这几乎肯定会导致阻塞/死锁，让你抓狂，而且这实际上甚至不会像预期的那样工作。在大多数情况下，您会收到前一个错误，这是因为**transaction scope 的任何显式超时值总是受到`maxTimeout`选项的限制，该选项可能在您的应用程序配置** :
中找到

```
<system.transactions>
     <!-- This property will supercede any values defined within your code -->
     <machineSettings maxTimeout="01:00:00" />
</system.transactions> 
```

默认的`maxTimeout`是 10 分钟，所以如果你开始遇到超时问题，你会想要在这个默认值和某个上限之间找到一个合适的设置。如果出现任何潜在的死锁/阻塞问题，请尽可能缩短超时时间。