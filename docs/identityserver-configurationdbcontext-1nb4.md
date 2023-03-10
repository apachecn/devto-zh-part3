# identity server -配置 dbcontext 中的跨上下文事务

> 原文：<https://dev.to/joni2nja/identityserver-configurationdbcontext-1nb4>

这篇文章最初出现在[媒体](https://medium.com/@joni2nja/identityserver-configurationdbcontext-%E3%81%A7%E3%81%AE%E3%82%AF%E3%83%AD%E3%82%B9%E3%82%B3%E3%83%B3%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E3%83%88%E3%83%A9%E3%83%B3%E3%82%B6%E3%82%AF%E3%82%B7%E3%83%A7%E3%83%B3-8b40f5019afc)上

* * *

### identity server-configurationdbcontext 下的跨上下文事务

#### 如何使用 ConfigurationDbContext 处理跨上下文事务？

[![](img/3b6b3211f39a809bdcf668aecaad496b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F5xU6O-P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/980/1%2AW94KJYyivaOCXCRJEo-gqA.png)

身份服务器とは？

> IdentityServer 是一个免费的开源 OpenID Connect 和 OAuth 2.0 框架，用于 ASP.NET 核心。IdentityServer4 由 Dominick Baier 和 Brock Allen 创建和维护，它包含了在应用程序中集成基于令牌的身份验证、单点登录和 API 访问控制所需的所有协议实现和扩展点。

详细情况请点击此处链接↓↓

[欢迎使用 identity server 4-identity server 4 1 . 0 . 0 文档](https://identityserver4.readthedocs.io/en/latest/)

您可以使用 EF Core 将 IdentityServer4 配置数据(配置数据)保存到数据库中。 配置数据管理“[资源](http://docs.identityserver.io/en/latest/intro/terminology.html#resources)”和“[客户端](http://docs.identityserver.io/en/latest/intro/terminology.html#client)”。

了解更多关于 EF Core 配置数据的信息↓↓

[使用 EntityFramework 核心进行配置和操作数据-身份服务器 4 1.0.0 文档](http://docs.identityserver.io/en/latest/quickstarts/7_entity_framework.html)

进入正题。

例如，如果业务规则需要，则需要在 APP 应用程序的 DB Context 中添加数据，同时需要添加客户端。 也就是说，需要成套追加处理。

客户端的类的定义存在于[ConfigurationDbContext](https://github.com/IdentityServer/IdentityServer4/blob/8f5a4ed926c5c76bf76d7397fae3ce2f4795fd74/src/EntityFramework.Storage/src/DbContexts/ConfigurationDbContext.cs) 中。 ctor 的定义是这样的:

```
public ConfigurationDbContext(DbContextOptions<TContext> options, ConfigurationStoreOptions storeOptions)
 : base(options) 
```

此 ConfigurationDbContext 是 IdentityServer 的一部分，与 APP 应用程序的 DB Context 不同，因此分别使用 dbContext.SaveChanges ( ) (或 await db context.)

EF Core 支持跨上下文事务，因此可以实现。

[交易- EF 核心](https://docs.microsoft.com/en-us/ef/core/saving/transactions#cross-context-transaction-relational-databases-only)

作为条件，

> 共享 DbConnection 需要在构造连接时能够将连接传递到上下文中。
> 
> 允许从外部提供 DbConnection 的最简单方法是停止使用 DbContext。OnConfiguring 方法来配置上下文，并在外部创建 DbContextOptions，并将它们传递给上下文构造函数。

然后，

> DbContextOptionsBuilder 是您在 DbContext 中使用的 API。在配置上下文时，您现在将在外部使用它来创建 DbContextOptions。

是的。

看起来很简单啊！

示例代码以这样的方式生成 DbContextOptions。

```
var options = new DbContextOptionsBuilder<BloggingContext>()
 .UseSqlServer(new SqlConnection(connectionString))
 .Options; 
```

把那个交给 ConfigurationDbContext ctor 就可以了吧~！ ！ 喜上眉梢

ctor 的第二个参数是传递 ConfigurationStoreOptions 吧。 在 DI 中不需要在意这个，因为在做，所以不知道正确的参数。 。 。 试着随便传递会发生错误。 虽然在调试等方面稍微有时间的话应该可以做到，但是我想用简单的方法做！

说起来，APP 应用程序的 DB Context 我们可以自由接触，所以先用它吧！ ctor 允许传递 DbContextOptions。

用我们的[示例](https://docs.microsoft.com/en-us/ef/core/saving/transactions#share-connection-and-transaction)来讲，context1 比喻为 ConfigurationDbContext，直接使用来自 DI 的实例，第 14 行的:

```
using (var context2 = new BloggingContext(options)) 
```

就这样让 context1 的 DB 连接和 context2 共享↓

```
using (var context2 = new ApplicationDbContext(
 new DbContextOptionsBuilder<ApplicationDbContext>()
 .UseSqlServer(context1.Database.GetDbConnection())
 .Options)) 
```

是的，这样很简单就解决了问题！