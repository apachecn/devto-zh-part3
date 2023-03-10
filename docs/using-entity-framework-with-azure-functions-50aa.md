# 使用带有 Azure 功能的实体框架

> 原文：<https://dev.to/azure/using-entity-framework-with-azure-functions-50aa>

<< Update: The instructions below are still good but I did update my sample to use .NET Core 3.1 here if interested: [https://github.com/jeffhollan/functions-chsarp-entitycore-3](https://github.com/jeffhollan/functions-chsarp-entitycore-3)>T3】

今年 Build 令人兴奋的发展之一是在 Azure 函数中支持[依赖注入](https://aka.ms/functions-di-docs)。这意味着您可以注册并使用自己的服务作为功能的一部分。虽然您过去已经能够使用 Entity Framework Core，但是与依赖注入的结合使得它更加适合。让我们创建一个简单的 Azure 函数，它可以使用实体框架核心与有状态数据进行交互。我将创建一个非常简单的 API，它可以在 Azure SQL 数据库中获取和设置博客数据。

## 向功能项目添加实体框架

首先，我创建了一个全新的 v2。NET Core 2) Azure Functions 项目。我选择了 HTTP 触发器模板作为开始。为了使用依赖注入，我需要验证两件事:

1.  `Microsoft.NET.Sdk.Functions`至少是`1.0.28`
2.  安装`Microsoft.Azure.Functions.Extensions`包来提供使用依赖注入的 API

```
Update-Package  Microsoft.NET.Sdk.Functions  Install-Package  Microsoft.Azure.Functions.Extensions 
```

现在，让我们安装实体框架库。⚠️:这些版本非常特别。你将运行的网络。因此，请慎重选择您的库版本。在撰写本文时，这个 Azure 正在运行。NET Core 2.2.3，这就是我要用的。

```
Install-Package  Microsoft.EntityFrameworkCore.SqlServer  -Version  2.2.3 
```

我还想利用设计时迁移，所以我将安装驱动它所需的工具。

```
Install-Package  Microsoft.EntityFrameworkCore.Design  -Version  2.2.3  Install-Package  Microsoft.EntityFrameworkCore.Tools  -Version  2.2.3 
```

### 添加实体模型

接下来，我将为我想要与之交互的数据模型创建一个非常简单的`DbContext`。让我们只关注简单的博客和帖子实体。

```
using Microsoft.EntityFrameworkCore;
using System;
using System.Collections.Generic;
using System.Text;

namespace functions_csharp_entityframeworkcore
{
    public class BloggingContext : DbContext
    {
        public BloggingContext(DbContextOptions<BloggingContext> options)
            : base(options)
        { }

        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Url { get; set; }

        public ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
} 
```

### 编写函数代码注入实体上下文

我需要验证我在函数类中使用了构造函数，这样我就可以注入正确的依赖项(在本例中，是实体框架上下文)。这意味着去掉 static 并添加一个构造函数。我还将修改构造函数来接受上面创建的`BloggingContext`(它本身需要注入一个`DbContextOptions<BloggingContext>`)。

```
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace functions_csharp_entityframeworkcore
{
    public class HttpTrigger
    {
        private readonly BloggingContext _context;
        public HttpTrigger(BloggingContext context)
        {
            _context = context;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            // Code that returns posts - you can see this code by going
            // to the full sample linked at the bottom
            return new OkResult();
        }
    }
} 
```

为了注册类似于`BloggingContext`的服务，我在项目中创建了一个`Startup.cs`文件，并实现了`FunctionsStartup`配置。请注意，我使用了一个属性来通知函数主机，它可以在那里运行我的配置。

```
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;
using System;

[assembly: FunctionsStartup(typeof(functions_csharp_entityframeworkcore.Startup))]

namespace functions_csharp_entityframeworkcore
{
    class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            string SqlConnection = Environment.GetEnvironmentVariable("SqlConnectionString");
            builder.Services.AddDbContext<BloggingContext>(
                options => options.UseSqlServer(SqlConnection));
        }
    }
} 
```

### 启用设计时 DbContext 创建

如上所述，我想使用[设计时 DbContext 创建](https://docs.microsoft.com/en-us/ef/core/miscellaneous/cli/dbcontext-creation)。由于我在这里没有直接使用 ASP.NET，而是实现了 Azure Functions `Configure`方法，所以实体框架不会自动发现所需的`DbContext`。所以我会确保实现一个`IDesignTimeDbContextFactory`来驱动工具。

```
public class BloggingContextFactory : IDesignTimeDbContextFactory<BloggingContext>
{
    public BloggingContext CreateDbContext(string[] args)
    {
        var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
        optionsBuilder.UseSqlServer(Environment.GetEnvironmentVariable("SqlConnectionString"));

        return new BloggingContext(optionsBuilder.Options);
    }
} 
```

### 获取项目。dll 放在正确的位置

我还需要做最后一件事。当您构建一个 Azure Functions 项目时，`Microsoft.NET.Sdk.Functions`会对构建工件进行一些组织，以创建一个有效的功能项目结构。其中一个。不幸的是，对于设计时工具，如实体框架迁移，它期望。dll 位于生成目标的根位置。因此，为了确保两个工具都满意，我将添加一个快速的后期构建事件来复制。dll 到根目录。我将这个添加到我的项目文件`.csproj`中。

```
<Target Name="PostBuild" AfterTargets="PostBuildEvent">
  <Exec Command="copy /Y &quot;$(TargetDir)bin\$(ProjectName).dll&quot; &quot;$(TargetDir)$(ProjectName).dll&quot;" />
</Target> 
```

## 添加实体框架迁移

我进入 Azure 门户，创建了一个简单的 Azure SQL 数据库。我将连接字符串复制到带有新值的`SqlConnectionString`文件中。您可以在我之前的代码示例中看到，我将它用作包含连接字符串的环境变量。这样我就不必将它签入源代码管理🥽.

为了确保 CLI 工具也可以使用连接字符串进行迁移，我将打开我的包管理器控制台，并在添加迁移和更新数据库之前设置环境变量。

```
$env:SqlConnectionString="Server=tcp:mySqlServerStuffxxx"  Add-Migration  InitialCreate  Update-Database 
```

当浏览 Azure 门户中的 SQL 数据库时，我可以看到表被成功地创建了！

[![Post create tables create](img/74847ac712e1760382c1d0f5012fa68d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E2bK_XNw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/PyDkh1p.png)

## 编写功能 app 并发布

那是所有的重活。现在我只编写了几个简单的 HTTP 触发函数，它们可以返回我注入的`BloggerContext`并向其中添加数据。当运行应用程序时，我可以调用不同的`GET`和`PUT`方法来验证数据是否被持久化并被正确返回。

发布之后，我只要确保用我的生产 SQL 连接字符串为`SqlConnectionString`设置合适的应用程序设置，我们就可以开始工作了！

你可以在我的 GitHub 账户上看到完整的项目代码