# 通过 Docker 向您的 ASP.Net 核心应用程序添加 PostgreSQL 支持

> 原文：<https://dev.to/nuculabs_dev/adding-postgresql-support-to-your-asp-net-core-application-via-docker-461>

我最近一直在玩 ASP.Net 核心，我正在开发一个小的 Web API 项目，以便自学这个框架。

在另一篇文章中，我将解释如何在 Docker 中使用 Postgres 数据库，以使您的开发体验更加愉快。

在继续之前，请确保您已经安装了:

*   [码头工人](https://www.docker.com/get-started)
*   [坞站-复合](https://docs.docker.com/compose/install/)

首先，在项目的根目录下创建`docker-compose.yml`文件，并用:
填充它

```
version: '3.4'
services:
  postgres:
    image: postgres
    environment:
      - POSTGRES_USER=app
      - POSTGRES_PASSWORD=app
      - POSTGRES_DB=mydbname
    volumes:
      - ./volumes/data/db:/var/lib/postgresql/data
    ports:
       - 5432:5432 
```

Enter fullscreen mode Exit fullscreen mode

这个特定的 postgres 配置将一些不言自明的环境变量传递给 postgres 数据库，它将。/volumes/data 目录添加到 postgres 的数据卷，并公开端口 5432。我们绑定卷，这样我们就可以实现数据的持久性，并浏览 postgres 数据库生成的文件。

接下来，将以下包添加到您的 ASP.Net 核心应用程序中:

```
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL.Design 
```

Enter fullscreen mode Exit fullscreen mode

将您的数据库连接配置添加到`appsettings.Development.json`文件中，注意它们与来自`docker-compose.yml`文件的字符串相匹配。在生产环境中运行时，您应该将每个服务部署在单独的容器中，并使用环境变量对它们进行配置。不要在你的文件中硬编码敏感信息！

```
{
"DatabaseConfig": {
    "PostgresSQL": "Server=localhost;Port=5432;Database=mydbname;User Id=app;Password=app;"
  },
...
} 
```

Enter fullscreen mode Exit fullscreen mode

现在在`Startup.cs`文件中，当配置应用程序上下文时，指定您想要使用 PostgresSQL。

```
services.AddDbContext<ApplicationContext>(options =>
{
    options.UseNpgsql(Configuration.GetSection("DatabaseConfig")["PostgresSQL"]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

就是这个！确保您的应用程序没有运行，将当前目录更改为项目的根目录，并使用`docker-compose up`启动数据库。数据库启动后，打开一个新的终端并添加迁移:

```
dotnet ef migrations add InitialMigration
dotnet ef database update 
```

Enter fullscreen mode Exit fullscreen mode

如果你想停止数据库，你可以运行`docker ps`和`docker stop <hash>`，其中`<hash>`是你的数据库容器的散列。按照上面的步骤，可以得到一个独立的开发数据库，可以与 ASP.Net 核心应用程序一起使用。如果您需要添加 ElasticSearch、Redis 或其他服务，您需要做的只是更改 docker-compose.yml 文件，以包含它们和 voilà。

感谢您的阅读，祝您愉快！