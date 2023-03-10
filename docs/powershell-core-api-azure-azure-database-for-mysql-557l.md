# PowerShell Core | API Azure |用于 MySQL 的 Azure 数据库

> 原文：<https://dev.to/ewertonjordao/powershell-core-api-azure-azure-database-for-mysql-557l>

#### 调配 MySQL 数据库资源消耗蓝方 API。

<figure>[![](img/28135b2b996c5d5e25790f5a6404f603.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MY6ConlK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ah2k0Bpmlh6Dkf8tuwgN8CQ.png) 

<figcaption>PowerShell 核心 Azure Rest API e MySQL</figcaption>

</figure>

嘿，伙计们？再一次谈到如何通过 PowerShell 创建蓝色资源，在另一篇文章中，我使用 PostgreSQL 列出的提供程序调配了一个资源，PostgreSQL 列出的提供程序最终会攻击 blue rest API 并创建一个或多个资源。在本文中，我们将通过发送 web 请求来设置 azure database for mysql。我们将了解如何检索订购信息、我们所处的环境等。这是一个很好的选择，可以避免多次登录以调配资源

注意:对于此演示，我将使用名为“**”的资源组进行免费测试如果您没有相同的资源组，则需要更改脚本的某些部分或创建资源组。另一个重要方面是成本，请访问该链接并查看[https://azure . Microsoft . com/en-us/pricing/calculator/？service=mysql](https://azure.microsoft.com/en-us/pricing/calculator/?service=mysql) 。**

考虑之后，我们将