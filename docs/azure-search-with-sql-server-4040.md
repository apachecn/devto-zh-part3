# 使用 SQL Server 的 Azure 搜索

> 原文：<https://dev.to/azure/azure-search-with-sql-server-4040>

*感谢[迈克尔·克伦普](https://twitter.com/mbcrump)创造了这个内容！*

**没有天蓝色？** [抢免费订阅](https://azure.microsoft.com/en-us/free?&WT.mc_id=azureapril_devto-blog-cxa)。

* * *

# 用 SQL Server 和 ASP.NET MVC 实现 Azure 搜索

今天的教程涵盖了 Azure 搜索和 ASP.NET MVC web 应用中的 SQL Server。

## 什么是[蔚蓝搜索](https://azure.microsoft.com/en-us/services/search/?&WT.mc_id=azureapril_devto-blog-cxa)？

Azure Search 是一个 SaaS——在这种情况下，搜索即服务——云解决方案，允许您访问 REST APIs(以及 SDK)来使用 web、桌面或移动应用搜索您的内容。

有各种各样的服务可以附加 Azure Search，包括 SQL Server。

## 创建索引器

这需要创建一个 SQL Server 数据库，我们将在其中存储索引器。

1.  进入 [Azure 门户](https://azure.microsoft.com/en-us/features/azure-portal/?&WT.mc_id=azureapril_devto-blog-cxa)。
2.  搜索“SQL Server”并点击`Add`按钮。
3.  给它一个数据库名称和资源组。
4.  填写新的服务器信息。
5.  单击附加设置选项卡。
6.  在“使用现有数据”中选择“样本”。这将使用`AdventureWorksLT`作为样本数据库。

[![Additional Settings, Select Sample](img/432f557d3020ba7a46524aa72bb3b65d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BbM8nrZx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xaxwli3lklk77cv5dp55.png)

> 记住！如果您想继续学习本教程，请仔细检查您是否选择了`AdventureWorksLT`数据库。
> 
> AdventureWorks 是我们专门为您构建的示例数据库！

## 窥视数据

现在我们有了一个示例数据库，它已经部署好了，让我们使用 SQL Server blade 中的[查询编辑器](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-connect-query-portal?&WT.mc_id=azureapril_devto-blog-cxa)来看看表结构。

> 如果您更喜欢，也可以使用 SQL Server Management Studio。

如果您点击`Query Editor`并按照指示登录，您将看到以下内容:

[![query editor](img/3121c165c3af20107f55744216168fbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TxW_wkrv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/1ebd0ad631fa85fe3ba6979de294fd86f46a36ed/bfc78/files/azuresearch2.png)

如您所见，我展开了`Table`下拉列表，可以再次深入到列名。

看起来,`Customer`将是实现搜索的一个很好的表，我们可以在`FirstName`或`LastName`字段中传递。

[![implement search](img/8a933a3935785e4d307d511ed8622dbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AmYulLJp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/da9a8012311620a4ad0ab0d1a443257a7d9dc7dd/cfd3f/files/azuresearch3.png)

尝试编写一个简单的查询来从`SalesLT.Customer`中选择`FirstName`和`LastName`。点击`Run`查看数据。

[![run](img/f9e806f8fd5cfce3c124ce10743a9983.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uET3x-nh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/3ecd830b0804ae0f3e4a06456f1882ca8e9281e2/6c48e/files/azuresearch4.png)

现在我们已经有了数据库和一些可靠的数据，我们将实现 Azure Search。

# 实现 Azure 搜索

让我们将 Azure Search 添加到现有的 SQL Server 实例中。

1.  打开 [Azure 门户](https://azure.microsoft.com/en-us/features/azure-portal/?&WT.mc_id=azureapril_devto-blog-cxa)。
2.  导航到您的 SQL Server 实例，从查看`Settings`窗格开始。

[![settings pane](img/cf02a74c8f7a5e4dade900dcdd8166dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0N_UaUxB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/b4cdb15066db05002a4875db8d5f1ace1b0588a3/f9d31/files/azuresearchsql1.png)

选择一个`Add Azure Search`并填写下面指定的字段，并确保选择自由价格。

[![add search](img/ff24f9d1f9616722ae1c5144e22520df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PNAHtwhG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/e543c245b51568117e1033f82ee68c3907276708/c2ad6/files/azuresearchsql2.png)

在`Data Source`下，我们可以很容易地连接到我们的 Azure SQL 数据库。

1.  给它起个名字。
2.  提供设置 SQL 数据库时指定的`userid`和`password`。
3.  按下`Test Connection`。
4.  如果一切顺利，那么您将能够选择`Customer`表。

[![customer table](img/c103221418eb2e21e6cfeb6e099047fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GHGwdkvm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/95722d60b0ab80ac38eed31958f8aefd5b5e5065/161cf/files/azuresearchsql3.png)

接下来，设置一个索引。

1.  给它起个名字。
2.  选择`CustomerID`作为您的键。
3.  通过删除任何不想要的字段来清理字段。
4.  确保字段可以被检索、排序、过滤和搜索，方法是添加一个类似下面照片的复选标记。

[![add a check](img/70735ef7207a5d3636f0056d237cc8d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g_7eFqs4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/1b77fbb13433e23465600a660a29fd3d95ae5fae/f9f9f/files/azuresearchsql4.png)

我们需要创建一个索引器。我将选择每日计划，并将我的水印列设置为`ModifiedDate`，因为我假设该列中的数据是唯一的。

[![create an indexer](img/0e88958d26c2e8de6d2da4d5e2a72743.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ValV6Xy5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/deebc5cb2f7ad177b8685c720005f988a198583a/60b80/files/azuresearchsql5.png)

一旦你开始，你会看到下面的通知。它说，你可以检查显示器的进展，一旦完成，你可以开始搜索。

[![notifications](img/3a5079362bd2f1ddb0f3019a077c2781.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kIXTfGKy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/3be1df9a667373e1f7c43341c7c66dd99357910a/31103/files/azuresearchsql6.png)

如果您继续并点击通知窗口上的链接，那么您将看到以下屏幕。

[![execution history](img/e397aad50fdc68ab168587f9a38cfeba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5ET5Ak5q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/892bda2b3da14e80e56d2341f80e1c9f6b6f01ae/d1364/files/azuresearchsql7.png)

继续按下`Run`按钮，它将立即启动。最终——预计时间会有所不同——您会看到它已经完成。

[![run it](img/129c58fb06ae53a1d3e9837980e463c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LKO1aAjB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/c8eed6b52ed093c8f3e5cd4880ff9bf542e4efa4/7ed41/files/azuresearchsql8.png)

太棒了。我们的 SQL 数据库和 Azure 搜索索引器已经就绪。

**想了解更多关于 Azure Search 的信息？**查看我们的[快速入门和教程](https://docs.microsoft.com/en-us/azure/search/?WT.mc_id=azureapril_devto-blog-cxa)！

* * *

我们将在四月份每天发布文章，敬请关注！或者跳到前面，查看更多提示和技巧[现在](http://azuredev.tips)。