# 使用 TransactionScope 平滑交易

> 原文：<https://dev.to/rionmonster/smooth-transactions-with-transactionscope-4i9o>

*这篇文章最初发表在[我的博客](http://rion.io)上。*

在现实世界中，我们无时无刻不在处理事务，尽管我们很少这样想。像开车去上班或为家人做晚餐这样的事情需要一系列事情都按预期进行，否则会发生不好的事情。我们越早认识到什么时候出了差错，我们就能越早让事情回到正轨。同样的道理也适用于软件，但是如果我们不小心的话，很难意识到哪里出了问题，直到为时已晚，我们的蛋卷里会有蛋壳。

事务可以是分解针对数据库的操作的一种方式，以确保一切按预期进行，如果没有，允许我们轻松地回滚更改，使我们的数据库保持良好的干净状态。问题是，要正确地实现它们可能会很麻烦，或者至少我认为是这样...

## 概括地说交易

那么，什么是软件意义上的事务呢？它实际上只是一个通常以数据库为目标的单一工作单元。如果事务中的所有步骤都成功了，那么就提交更改，但是如果一个或多个步骤失败了，就好像什么都没发生一样。

[![smooth transaction](img/b994257e79591b55bb95f8e59011348a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KSZCUlc1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/99iu0aaxoagoghdn8boz.gif)

考虑任何多阶段操作，比如在线下订单。结帐可能需要一系列事件才能成功(例如，验证信用卡信息、确保商品有库存等)。)如果其中任何一项失败(即你没有钱)，那么订单就不会通过。

这是一个足够简单的概念，但是实现它通常是一件痛苦的事情。

## 处理所有的~~事物~~技术

实现事务将取决于您的后端/数据层是什么样的。如果你依赖的是实体框架之类的框架，你会免费得到它(例如`context.SaveChanges()` ):

```
using (var context = new WidgetContext())
{
      context.Widgets.Add(new Widget(){ Name = "Example" });
      // This statement will commit the transaction (or blow up and rollback 
      // if something goes wrong)
      context.SaveChanges();                
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，如果您是一个 SQL 密集型企业，您可能只在存储过程或查询中实现它们:

```
BEGIN TRANSACTION SomeTransaction

  BEGIN TRY
      INSERT INTO dbo.Widgets ([Name]) 
      VALUES      ('Example')

      UPDATE dbo.Widgets
      SET    [Name] = CONCAT([Name], '_Updated')
      WHERE  [Name] = 'Example'

      -- This will commit the changes
      COMMIT TRANSACTION SomeTransaction
  END TRY
  BEGIN CATCH
      -- If anything goes wrong, this will rollback the changes to the 
      -- previous state
      ROLLBACK TRANSACTION SomeTransaction
  END CATCH 
```

Enter fullscreen mode Exit fullscreen mode

在某些情况下，您可能没有那么幸运将所有数据放在一个篮子里。如果你的应用程序需要对不同过程的不同调用，通过 Dapper 的原始 SQL 查询，可能一些对 SSAS、Azure 等的调用。而且你需要所有这些东西协同工作，你需要事务。

传统上，事务在连接级实现，并传递给多个底层调用:

```
using (var connection = new SqlConnection("..."))
{
     using (var transaction = connection.BeginTransaction())
     {
            // Now pass your specific connection (and transaction) to 
            // your other calls to operate on
            DoSomething(connection, transaction);
            DoSomethingElse(connection, transaction); 
     }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所想象的，这可能会变得非常复杂，尤其是考虑到您的事务与您的连接相关联，如果您与多个连接进行交互，这可能会非常复杂。此外，您还负责管理所有单独的连接和事务，这可能会混淆您的方法签名(特别是如果这些方法中的一些在多个地方被调用，或者不希望传入一个连接)。

您可能会尝试通过将这个问题转移到客户端并依靠将各个操作链接在一起的承诺来解决这个问题，但是同样，如果您想回滚一些事情(例如，第 3 步失败，因此分别回滚第 2 步和第 1 步)，这就有点麻烦了。您可能最终会实现一些完全独立的端点集来调用一些东西来撤销前面的步骤，或者通过会话/cookie 在服务器上保存一些东西来跟踪正在发生的事情。

此时，我们有三个“meh”选项:

*   在不同的方法之间传递事务(如果涉及多个连接，使用语句等，则很脆弱)。)
*   为我们流程的每一步链接客户端承诺(不会自己完成事务，回滚会很脏/很臭)
*   全部用 SQL 处理(不总是可行的，可能需要大的重构)

在这一点上，你可能会问自己“一定有更好的方法”，和往常一样:有。

## 用魔法划定范围

[![its magical](img/dd46923252db6bb663e394b0290ffe6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MVyD2Cjt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f7ts1ptwip6mmvaqwjzz.gif)

这些通常可以在 SQL 查询中实现，但是在这篇文章中，我将主要讨论在 C#中与它们交互，这通常很有挑战性。

在处理跨越多个连接、数据库、技术的场景时:事情会变得非常棘手。跟踪一个事务对象，确保连接不会过早关闭，上帝禁止事情变得不稳定，但事情不一定非要这样。名称空间`System.Transactions`公开了一个名为 [`TransactionScope`](https://docs.microsoft.com/en-us/dotnet/api/system.transactions.transactionscope) 的类，使得这一切像魔术一样工作。

基本上，您只需要将各个不同的调用包装在 TransactionScope 实例中，它会将对数据源的底层调用包装成事务:

```
// This will create a new transaction and associate all underlying calls to it
using (var scope = new TransactionScope())
{
       try
       {
           // It doesn't matter where you are pulling your data from, if
           // transactions are supported there TransactionScope will take
           // care of applying them within the TransactionScope block if 
           // things go south.
           GetSomeDataFromSqlServer();
           GetSomeDataFromAzure();
           GetSomeDataFromEntityFramework();
           GetSomeDataFromMongoDB();
       }
       catch (TransactionAbortedException ex)
       {
             // At this point - all of the operations that have already 
             // been performed prior to the failure will be rolled back
       }
} 
```

Enter fullscreen mode Exit fullscreen mode

打 Azure？SQL Server？实体框架？没关系，如果存在一个执行事务的构造，它就会工作。此外，如果在您的事务中有您不想包含的特定调用(即总是执行)，您可以显式地取消这些调用:

```
using (var scope = new TransactionScope())
{
      // Transact all of these things
      SomeTransactedCall();
      SomeOtherTransactedCall();

      // Some readonly operation that we don't care to transact
      using(var suppressedScope = new TransactionScope(TransactionScopeOption.Suppress))  
      {  
           SomeCallThatIsNotTransacted();
      }  
} 
```

Enter fullscreen mode Exit fullscreen mode

就是它，它似乎像魔法一样起作用！但是，当出现问题时，当您的数据可能会/可能不会被正确删除时，您并不总是希望发生神奇的事情。因此，让我们透过窗帘看看到底发生了什么，以及这是如何工作的！

## 从窗帘后窥视

虽然`TransactionScope`最初可能看起来像是纯粹的魔术，但如果你遇到问题，知道幕后实际发生了什么是很重要的。

`TransactionScope`的实例本身实际上是通过[在幕后管理一个环境静态事务](https://referencesource.microsoft.com/#System.Transactions/System/Transactions/Transaction.cs,64d04d814ca6c165)来工作的。这与实际提供事务资源的所有类(例如 SqlConnections、实体框架数据上下文等)协同工作。)并允许那些资源利用在事务范围内创建的环境事务来登记它们的事务:

```
// This is going to create an ambient transaction behind the scenes and manage it
// throughout the scope of this block (i.e. any operations that support transactions
// with be associated with the underlying ambient transaction
using (var ambientTransaction = new TransactionScope())
{
      // Since the System.Data.SqlClient assembly supports transactions, any
      // operations performed by it within the scope of the TransactionScope
      // object will subscribe to the ambient transaction so that if this call
      // "fails", the ambient transaction will "fail" and thus rollback any 
      // previous operations
      using (var sqlConnection = new SqlConnection(...))
      {
            // Do some SQL Server operations here
      }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您在上一节的最后一点中看到的，您可以为给定的事务范围定义一个特定的`TransactionScopeOption`属性来使用。这是决定事务是否应该与底层环境事务相关联的关键组件(即，`TransactionScopeOption.Suppress`将忽略对该范围内任何事务的跟踪，因此它们不会被添加到任何现有环境事务中)。

**基本上——任何在一个`TransactionScope`块内*打开的连接都会自动使用底层环境事务，除非被明确告知不要这样做。***这里的关键短语是内的*，因为在交易之前已经打开的任何连接都不会自动参与。这非常重要，如果您已经在整个应用程序中共享/传递连接，可能需要进行重大的重构。*

在使用`TransactionScope`时，有相当多的注意事项和潜在的问题会被联系起来，这将在以后的一篇文章中详细阐述，并讨论许多解决方案/变通办法。但是在大多数情况下——它应该在大多数常见场景中“正常工作”。