# 使用 Odata 公开可查询的 api

> 原文：<https://dev.to/jannikbuschke/exposing-a-queryable-api-with-odata-14ef>

**TL；dr:** Odata 提供了一个很好的、传统的、强大的、易于设置的 api。这篇文章是一个展示只读模型的演练。

我们将

1.  初始化新的 web api 项目
2.  为 ASP.NET 核心和微软 api 版本库添加 Odata
3.  在`Startup.cs`类中，配置服务和中间件管道以启用 Odata
4.  添加示例模型和 Odata 控制器
5.  实现`IModelConfiguration`来配置 Odata 模型
6.  对 Odata 控制器执行一些查询

github 上提供了最终的源代码:

```
git clone https://github.com/jannikbuschke/pragmatic-cqrs-with-asp-net-core-for-spas 
```

* * *

## 简介

这是关于在 Odata 的帮助下创建一个固执己见的 ASP.NET 核心 api 的迷你系列的第一篇文章。

Odata 是一个*传统的* restful api，这意味着一些查询操作如`top, skip, select, order by`和`filter`(典型的类似 sql 的操作)被标准化为 api 的一部分。Odata 还为*命令*(如果我们从`CQRS`的角度考虑的话)提供了约定，我将省略这一部分，因为根据我的经验，这部分使用起来过于复杂，而普通的 webapi 功能强大，很容易调整/修改以提供自定义约定。

这篇文章介绍了如何设置一个简单的可查询(只读)api，在我看来这是最“物有所值”的。

### 具有 ASP.NET 核心的实用 CQRS 和 SPAs -系列概述

1.  用 Odata 公开一个可查询的 API(this)
2.  [用 Odata 增强 ASP.NET 核心 api 控制器](https://www.jannikbuschke.de/blog/webapi-enable-query/)
3.  [向客户展示 ASP.NET 核心验证](https://www.jannikbuschke.de/blog/expose-aspnetcore-validation/)
4.  创建针对(SPA)表单优化的自以为是的/传统的 api(组合上述方法)

* * *

## 项目设置

让我们创建项目并添加 odata 以及 mvc 和 Odata 版本控制。

```
dotnet new webapi --name MyApp
cd MyApp
dotnet package add Microsoft.AspNetCore.Odata
dotnet package add Microsoft.AspNetCore.Mvc.Versioning
dotnet package add Microsoft.AspNetCore.OData.Versioning 
```

我们的。csproj 文件现在应该是这样的:

```
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
    <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.AspNetCore.Odata" Version="7.1.0" />
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Versioning" Version="3.1.2" />
    <PackageReference Include="Microsoft.AspNetCore.OData.Versioning" Version="3.1.0" />
    <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.2.0" PrivateAssets="All" />
  </ItemGroup>

</Project> 
```

在`ConfigureServices`方法(Startup.cs)中添加 Odata 和 ApiVersioning 服务:

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvcCore(options =>
    {
        options.EnableEndpointRouting = false;
    });

    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    services.AddApiVersioning();
    services.AddOData().EnableApiVersioning();
} 
```

还要在`Configure`方法(Startup.cs)中添加 Odata 路由。`Configure`方法需要一个`VersionedODataModelBuilder`作为附加参数:

```
public void Configure(
    IApplicationBuilder app,
    IHostingEnvironment env,
    VersionedODataModelBuilder modelBuilder
)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseMvc(builder =>
    {
        builder.Select().Expand().Filter().OrderBy().Count();
        builder.MapVersionedODataRoutes("odata", "odata", modelBuilder.GetEdmModels());
    });
} 
```

## 添加型号、配置和控制器

接下来创建一个模型(Person.cs):

```
public class Person
{
    public Guid Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
} 
```

和 Odata 配置(odatamodelconfigurations . cs)

```
public class OdataModelConfigurations : IModelConfiguration
{
    public void Apply(ODataModelBuilder builder, ApiVersion apiVersion)
    {
        builder.EntitySet<Person>("Persons");
    }
} 
```

最后，让我们用动态创建的样本数据创建一个控制器(PersonsController.cs):

```
public class PersonsController : ODataController
{
    [EnableQuery]
    public IQueryable<Person> Get()
    {
        return new string[]
            { "Alice", "Bob", "Chloe", "Dorothy", "Emma", "Fabian", "Garry", "Hannah", "Julian" }
            .Select(v => new Person { FirstName = v, Id = Guid.NewGuid(), Age = new Random().Next(80) })
            .AsQueryable();
    }
} 
```

如果你有使用`Entity Framework Core`的经验，你可以用上面的`Get()`方法返回你的`DbContext`实现的一个`DbSet<T>`(或`DbQuery<T>`)属性。

## 运行和测试

现在我们有了一个控制器，我们可以在浏览器中运行项目和执行查询:

```
dotnet run 
```

在浏览器中打开`https://localhost:5001/odata/Persons?api-version=1.0`(或`http://localhost:5000/...`)。

您应该获得 json 格式的完整结果集。

现在可以尝试的一些参数:

```
/odata/Persons?api-version=1.0&$top=5                                   // first page with pagesize 5
/odata/Persons?api-version=1.0&$top=5&$skip=5                           // second page
/odata/Persons?api-version=1.0&$orderby=age                             // order by age ascending (young to old)
/odata/Persons?api-version=1.0&$orderby=age desc                        // order by age descending
/odata/Persons?api-version=1.0&$filter=startswith(firstName,'G')        // only show Persons whos firstname starts with 'G'
/odata/Persons?api-version=1.0&$filter=contains(firstName,'e')          // only show Persons whos firstName contains an 'e'
/odata/Persons?api-version=1.0&$filter=age gt 20 and age lt 50          // only show Persons older than 20 and younger than 50 
```