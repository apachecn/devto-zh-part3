# 第 011 集-实体框架核心的数据访问-ASP.NET 核心:从 0 到过度

> 原文：<https://dev.to/joaofbantunes/episode-011---data-access-with-entity-framework-core---aspnet-core-from-0-to-overkill-25cn>

在这一集里，我们用一个实际的数据库替换我们当前组的“持久性”,用实体框架核心访问它。对于演练，您可以查看接下来的几个视频，但是如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/gb2SOufcbQg](https://www.youtube.com/embed/gb2SOufcbQg)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。

## [T1】简介](#intro)

在本帖中，我们将替换我们一直使用的内存“持久化”。我们将使用 PostgreSQL 数据库，使用实体框架核心来实现数据访问代码。

### 储存库模式

我们现在不会实现存储库模式，而是直接在业务层使用 EF。有人说使用 EF 时并不需要实现存储库模式，因为它已经实现了——一个`DbContext`就像一个工作单元，一个`DbSet`就像一个存储库。

尽管这是真的，并且 EF 有一些允许测试的设施，我认为实现存储库模式更简化了测试，当然，保持代码与具体使用的 ORM 分离。看看史蒂夫·史密斯在[“024:我需要一个存储库吗？”](http://www.weeklydevtips.com/024)。

这和软件开发中的大多数情况一样，归结为妥协，如果你觉得实现这个模式不值得做额外的工作，并且与 ORM 更好的结合并不令人担忧，那就继续吧。

### 运行 PostgreSQL

我在 Docker 容器中使用 PostgreSQL，但当然这是不需要的，我这样做只是为了保持计算机干净，而不是安装我们想要测试的所有东西，我们可以只安装 Docker，只在容器中运行我们想要测试的东西。如果您想尝试一下，我用来启动 PostgreSQL 容器的命令如下。

`docker run -p 5432:5432 --restart unless-stopped --name postgres -e POSTGRES_USER=user -e POSTGRES_PASSWORD=pass -d postgres`

我不会说更多的细节，因为我们稍后会讨论 Docker。

## 数据访问项目

为了保留组管理服务的所有数据访问代码，我们创建了一个新的类库项目(targeting。网标 2.0)，叫`CodingMilitia.PlayBall.GroupManagement.Data`。对于这个新项目，我们需要添加几个依赖项:

*   `Microsoft.EntityFrameworkCore` -很容易被识别为实体框架核心的包
*   `Npgsql.EntityFrameworkCore.PostgreSQL` -允许我们将 EF 核心与 PostgreSQL 数据库一起使用

## 创建实体和 DbContext

到目前为止我们只有一个实体，那就是集团。因此，我们在新项目中创建一个名为`Entities`的文件夹，并向其中添加一个`GroupEntity`类。

`GroupEntity.cs`

```
public class GroupEntity
{
    public long Id { get; set; }
    public string Name { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能认识这段代码，因为它与我们在 API 和业务层中的代码基本相同。

现在为了创建允许我们与数据库“对话”的`DbContext`，我们在项目的根目录中添加了一个名为`GroupManagementDbContext`的新类，它继承自`DbContext`。

`GroupManagementDbContext.cs`

```
public class GroupManagementDbContext : DbContext
{
    public DbSet<GroupEntity> Groups { get; set; }

    public GroupManagementDbContext(DbContextOptions<GroupManagementDbContext> options) : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasDefaultSchema("public");

        // Configure entities ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个类中，我们为我们希望在上下文中可用的每个实体创建了一个类型为`DbSet<T>`的属性，现在只有`GroupEntity`。

我们还创建了一个接收`DbContextOptions<GroupManagementDbContext>`的构造函数，这将允许我们稍后使用提供的扩展方法在依赖注入容器中设置`DbContext`。

最后我们有`OnModelCreating`，它允许我们配置实体的各个方面，比如特定的列映射、键、索引等等，如果约定还不够的话。

注意，在`OnModelCreating`的第一行，我们用`"public"`来调用`HasDefaultSchema`。老实说，我不确定是否还需要这个，也许我应该测试一下，确定一下😛，但我已经习惯使用它的原因是因为默认情况下，在 SQL Server 中，模式是`dbo`，而在 Postgres 中是`public`，我记得在过去有这个问题(甚至在。网核存在)。

现在，为了配置实体，我们有几个选项——数据注释和 fluent API。我更喜欢流畅的 API，因为它允许实体是干净的，没有 EF 特有的“污染”。要使用 fluent API，所有代码通常都直接放在`OnModelCreating`方法中，这很快就变得很难看。但是最近，我想在 EF Core 2.0 中，有了`IEntityTypeConfiguration<T>`接口，我们可以为每个想要配置的实体实现这个接口。在一个名为`Configurations`的新文件夹中，我们添加了一个新类`GroupEntityConfiguration`。

`GroupEntityConfiguration.cs`

```
internal class GroupEntityConfiguration : IEntityTypeConfiguration<GroupEntity>
{
    public void Configure(EntityTypeBuilder<GroupEntity> builder)
    {
        builder
            .HasKey(e => e.Id);

        builder
            .Property(e => e.Id)
            .UseNpgsqlSerialColumn();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于`Configure`方法的第一行，我们说`Id`属性，或者更确切地说，它将映射到的`Id`列，将是表的主键。这并不是真正需要的，因为按照惯例，EF 会假设一个名为`Id`的属性是这样的，但是这并没有坏处🙃

紧接着，我们说`Id`列应该是一个`SERIAL`列，它是自动分配的，并在每次插入剧透警告时递增，后来我发现我应该使用`UseNpgsqlIdentityAlwaysColumn`来代替，它的作用就像我们在 SQL Server 的`IDENTITY`列中习惯的那样。

为了总结模型配置，我们需要使用这个`GroupEntityConfiguration`类。我已经做了最简单的事情，通过在`OnModelCreating`中手动添加它，但是如果有许多实体，这会很快变得乏味，所以也许一些反射诡计来自动发现这些类会很好，但是现在，保持简单。

`GroupManagementDbContext.cs`

```
public class GroupManagementDbContext : DbContext
{
    //...

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasDefaultSchema("public");
        modelBuilder.ApplyConfiguration(new GroupEntityConfiguration());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 适应业务层

既然我们已经准备好了(大规模复杂的)实体和`DbContext`,我们可以调整业务层来使用数据库而不是“内存持久化”。

界面没有什么真正的改变，但是我们现在可以删除`InMemoryGroupsService`类并创建一个新的名为`GroupsService`的类(也想到了`EfGroupsService`，但是我认为不需要额外的东西)。

首先，我们创建一个接收`GroupManagementDbContext`实例的构造函数。

`GroupsService.cs`

```
public class GroupsService : IGroupsService
{
    private readonly GroupManagementDbContext _context;

    public GroupsService(GroupManagementDbContext context)
    {
        _context = context;
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以从读取方法开始，`GetAllAsync`和`GetByIdAsync`。

`GroupsService.cs`

```
public class GroupsService : IGroupsService
{
    // ...

    public async Task<IReadOnlyCollection<Group>> GetAllAsync(CancellationToken ct)
    {
        var groups = await _context.Groups.AsNoTracking().OrderBy(g => g.Id).ToListAsync(ct);
        return groups.ToService();
    }

    public async Task<Group> GetByIdAsync(long id, CancellationToken ct)
    {
        var group = await _context.Groups.AsNoTracking().SingleOrDefaultAsync(g => g.Id == id, ct);
        return group.ToService();
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

关于这些的一些注释:

*   我们使用`_context`来访问`Groups` `DbSet<Group>`，后者作为存储库来查询数据库。
*   在`GetAllAsync`上，我们按照`Id`进行排序，只是为了保持一致的顺序，就像我们没有排序一样，这将是数据库引擎所希望的，最终可能会不一致。
*   注意`AsNoTracking`方法调用。这告诉 EF，我们不需要它来跟踪获取的实体，因为我们将把它们用于只读目的。这是一种优化，因为我们避免 EF 做超过我们需要的工作。
*   `ToService`方法与我们在前几集使用的`ToViewModel`非常相似，在这种情况下，将数据库实体转换为其服务表示。

`UpdateAsync`和`AddAsync`也很简单，但是特别的是`UpdateAsync`有一些需要特别注意的地方。

`GroupsService.cs`

```
public class GroupsService : IGroupsService
{
    // ...

    public async Task<Group> UpdateAsync(Group group, CancellationToken ct)
    {
        var updatedGroupEntry = _context.Groups.Update(group.ToEntity());
        await _context.SaveChangesAsync(ct);
        return updatedGroupEntry.Entity.ToService();
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我认为这是实现更新操作的最简单和最有效的方法，但是需要记住一些事情:

*   我们可以先获取实体，然后在调用`SaveChangesAsync`之前更新它的属性，但这意味着对于一次更新，我们将对数据库进行两次往返。如果我们能在一次往返中完成，那就更好了。
*   我们可以对作为参数得到的`group`做`_context.Groups.Update`，因为它有所有的实体数据。如果我们只想进行部分更新，我们需要在更新之前从数据库中获取现有的实体。
*   做`_context.Groups.Update`相当于做`_context.Entry(entity).State = EntityState.Modified`，也就是说将实体添加到 EF tracker 中，并标记为修改。如果我们已经在上下文中有了实体(例如，因为没有`AsNoTracking`的提取)，我们将得到一个`InvalidOperationException`。

最后，我们有`AddAsync`，它看起来就像`UpdateAsync`，只是使用了`DbSet`的`Add`方法而不是`Update`。

`GroupsService.cs`

```
public class GroupsService : IGroupsService
{
    // ...

    public async Task<Group> AddAsync(Group group, CancellationToken ct)
    {
        var addedGroupEntry = _context.Groups.Add(group.ToEntity());
        await _context.SaveChangesAsync(ct);
        return addedGroupEntry.Entity.ToService();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在 web 应用中配置 EF

准备好实体和使用它们的业务层后，我们需要配置 web 应用程序来使用它们，即在 DI 容器中注册`DbContext`并向配置中添加连接字符串。

这里的变化很小。让我们从 DI 注册开始，我们只需要使用`AddDbContext`扩展方法。

`Startup.cs`

```
public class Startup
{
    // ...

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();

        services.AddDbContext<GroupManagementDbContext>(options =>
        {
            options.UseNpgsql(_config.GetConnectionString("GroupManagementDbContext"));
        });

        services.AddBusiness();
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们向`AddDbContext`提供我们创建的上下文的具体类型，并使用 options builder 使它使用 PostgreSQL 和我们仍然需要添加到配置中的连接字符串。`GetConnectionString`扩展方法假设有一个名为`ConnectionStrings`的配置部分，然后获取我们提供的名称的值。

理想情况下，连接字符串应该被视为一个秘密，因此是使用秘密管理器工具的一个很好的候选，但是由于我们只是使用一个本地开发数据库，使用最简单的凭证，将连接字符串放在`appsettings.development.json`文件中更容易。

`appsettings.development.json`

```
{  "OtherSections":  "...",  "ConnectionStrings":  {  "GroupManagementDbContext":  "server=localhost;port=5432;user id=user;password=pass;database=GroupManagement"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，在启动时，应用程序将能够读取配置并将其传递给`DbContext`设置。

## 迁徙

### 什么是迁移

在运行应用程序之前，我们需要创建允许我们创建/更新数据库模式的迁移。

更准确地说，我们不需要迁移就可以开始，我们只需要在应用程序启动时做`dbContext.Database.EnsureCreated`就可以了，它会根据当时我们的实体配置的状态来创建数据库。我们甚至可以在`EnsureCreated`之前做`EnsureDeleted`(也来自`dbContext.Database`)，所以我们总是有一个干净的数据库，但是这只对开发/测试环境有用。

另一方面，迁移允许我们保留对数据库模式所做的各种更改，这在我们将数据库投入生产并希望更新它以满足新的数据需求时非常重要。每次我们向数据库应用迁移时，都会保留有关已应用迁移的信息，因此下次我们需要更改模式时，只会应用新的迁移。在数据库中，您会看到一个名为`__EFMigrationsHistory`的表，其中包含了已应用迁移的历史。

### 创建迁移

如果我们不使用实体框架核心(或另一个 ORM/迁移工具)，我们可能需要手动实现迁移，即编写 SQL 脚本来更改数据库模式。但是当使用 EF 迁移时，我们只需要利用命令行工具来创建迁移，它将生成脚本——在这种情况下是 C#脚本，在应用迁移时，这些脚本被翻译成 SQL 通过比较实体的当前状态和上次创建迁移时的状态(在我们创建第一次迁移时不存在)来推断变化。

为了创建迁移，我们在命令行中进入数据库项目文件夹，并输入`dotnet ef migrations add InitialCreate`。

假设这将创建一个迁移，但是我们得到一个错误:

```
Startup project 'CodingMilitia.PlayBall.GroupManagement.Data.csproj' targets framework '.NETStandard'. There is no runtime associated with this framework, and projects targeting it cannot be executed directly. To use the Entity Framework Core .NET Command-line Tools with this project, add an executable project targeting .NET Core or .NET Framework that references this project, and set it as the startup project using --startup-project; or, update this project to cross-target .NET Core or .NET Framework. For more information on using the EF Core Tools with .NET Standard projects, see https://go.microsoft.com/fwlink/?linkid=2034781 
```

Enter fullscreen mode Exit fullscreen mode

因此，要总结这面文字墙，要运行迁移，我们需要一个目标项目。NET 框架或者。网核，as。NET 标准不能直接运行。有不同的方法来解决这个问题，但我只是采用了建议的解决方案，并使用 web 项目(`CodingMilitia.PlayBall.GroupManagement.Web`)进行启动，如下所示:

`dotnet ef migrations add InitialCreate -s ../CodingMilitia.PlayBall.GroupManagement.Web/CodingMilitia.PlayBall.GroupManagement.Web.csproj`

使用主项目作为迁移的启动，好处是能够重用已经在其中的配置来运行迁移，即依赖注入`DbContext`配置，因为创建迁移需要这种或另一种受支持的方式来创建`GroupManagementDbContext`(更多关于这个[在这里](https://docs.microsoft.com/en-us/ef/core/miscellaneous/cli/dbcontext-creation))。

## 确保数据库最新

到目前为止，我们已经做了相当多的工作，几乎可以开始应用程序了。总而言之，我们创建了实体，创建了`DbContext`，准备了使用 EF 的业务逻辑，并创建了迁移来保持数据库与代码同步。现在，在启动应用程序之前，我们需要应用我们创建的迁移。

我能想到 3 种可能性来做这件事(尽管可能有更多):

1.  使用 EF 的命令行工具运行迁移
2.  创建助手应用程序来运行迁移
3.  web 应用程序启动时运行迁移

### 使用 EF 的命令行工具运行迁移

第一个选项是最简单的，当我们创建迁移并想要测试它们时，它是最好的。在这种情况下，我们只需执行以下命令来应用迁移:

```
dotnet ef database update -s ..\CodingMilitia.PlayBall.GroupManagement.Web\CodingMilitia.PlayBall.GroupManagement.Web.csproj 
```

Enter fullscreen mode Exit fullscreen mode

就需要指定一个可运行的启动项目而言，它非常类似于创建迁移的命令。

这种方法很好，但是因为我在 Docker 中运行数据库，并且总是删除和创建容器/数据库，总是手动运行这种方法很烦人，所以我想要一个更加自动化的工作流。

### 创建助手应用程序来运行迁移

虽然我不打算创建这个选项的示例，但我想提一下，因为这是我的一个同事提出的一个好主意。

其要点是创建一个简单的控制台应用程序，它所要做的就是应用迁移。这很好，因为我们可以向某人提供二进制文件，这只是运行可执行文件的问题。

虽然我在这个项目中没有采用这种方法，但是实现它的代码与第三种方法非常相似，主要区别是这一种方法是在独立的迁移应用程序中，而另一种方法包含在 web 应用程序本身中。

### web 应用程序启动时运行迁移

确保我们拥有最新数据库的最好方法——至少在开发中，在生产中这可能不是最好的主意——是在应用程序启动时运行迁移。这样，每当我们对数据库进行更改时，当我们启动应用程序来测试它们时，它们将被自动应用。

在 web 应用程序项目中，我们创建了一个名为`StartupHelpers`的新文件夹，然后创建了一个`DatabaseExtensions`静态类，使用方法`EnsureDbUpToDate`(它缺少了`Async`后缀，我会在记起来的时候修复它😛).

`DatabaseExtensions.cs`

```
namespace Microsoft.AspNetCore.Hosting
{
    internal static class DatabaseExtensions
    {
        internal static async Task EnsureDbUpToDate(this IWebHost host)
        {
            using (var scope = host.Services.CreateScope())
            {
                var context = scope.ServiceProvider.GetRequiredService<GroupManagementDbContext>();
                await context.Database.MigrateAsync();
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该方法是对`IWebHost`的扩展，因为我们需要解决依赖关系，并且在应用程序启动之前，这是这样做的，因为我们可以访问包含在`Startup.ConfigureServices`中配置的依赖关系的`IServiceProvider`。

在我们从提供者那里获得`DbContext`之前，我们需要创建一个范围。如果我们不创建一个作用域，那么被解析的`DbContext`将作为一个单体存在于 DI 容器中。通过创建一个作用域，我们在处理请求时基本上做了与框架相同的事情，创建了一个作用域，它在请求到达时开始，在响应被发回时被释放。

现在有了作用域，我们可以在它的上下文中得到另一个`IServiceProvider`,它将为我们提供正确作用域的依赖关系。我们可以使用
`GetRequiredService<T>`或`GetService<T>`，这是前者在我们请求的问题无法解决时抛出的区别，而后者则没有。

在所有这些之后，为了尽可能最好地使用 DI 容器，我们获取`DbContext`并可以调用`context.Database.MigrateAsync`来应用任何挂起的迁移。

顺便提一下，我想我已经在本系列的上一篇文章中指出过，看看名称空间。这不是真正需要的，它只是有助于发现，因为在我们有`IWebHost`的代码段中，我们不需要任何额外的`using`，我们将立即看到我们新创建的方法。

总结一下，我们需要调用新方法，因此我们更改了`Program`类`Main`方法。

`Program.cs`

```
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        await host.EnsureDbUpToDate();
        host.Run();
    }

    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，方法签名改变了，现在它是一个异步方法。为了能够使用这个可选的 main 方法签名，我们需要使用 C# 7.1 或更高版本。为了拥有它，我简单地将`<LangVersion>7.3</LangVersion>`添加到`csproj`文件中，就在框架版本(`<TargetFramework>netcoreapp2.2</TargetFramework>`)的下面。

然后，我们调用(等待)新的`EnsureDbUpToDate`方法，而不是立即运行主机。

现在我们终于可以运行这个应用程序了，除了拥有真正的持久性之外，它的行为和引入 EF Core 之前一样🙂。

## 乐观并发

在结束之前，我想谈的最后一个主题是使用乐观并发机制来最小化并发数据库更改问题的可能性，同时避免使用锁。

为了实现这一点，我们将向`GroupEntity`添加一个属性作为并发令牌。有了它，每次我们做更新时，EF 将检查令牌是否改变了，如果改变了，更新将不会进行，并将抛出一个`DbUpdateConcurrencyException`异常。

对于`GroupEntity`类，我们添加了一个名为`RowVersion`的新属性。

`GroupEntity.cs`

```
public class GroupEntity
{
    public long Id { get; set; }
    public string Name { get; set; }
    public uint RowVersion { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我使用的类型(`uint`)非常具体，因为我使用的是 PostgreSQL。在 SQL Server 中，通常的类型是一个`byte[]`(关于开放式并发的更多信息，特别是关于 SQL Server 的例子，请看一下[这些文档](https://docs.microsoft.com/en-us/ef/core/modeling/concurrency))。

然后我们必须告诉 EF 使用新的属性作为并发令牌。这是在`GroupEntityConfiguration`类中完成的。

`GroupEntityConfiguration.cs`

```
internal class GroupEntityConfiguration : IEntityTypeConfiguration<GroupEntity>
{
    public void Configure(EntityTypeBuilder<GroupEntity> builder)
    {
        //...

        builder
            .Property(e => e.RowVersion)
            .HasColumnName("xmin")
            .HasColumnType("xid")
            .ValueGeneratedOnAddOrUpdate()
            .IsConcurrencyToken();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 PostgreSQL 将属性配置为并发令牌的代码比使用 SQL Server 要麻烦一些(在 SQL Server 中，调用`IsRowVersion`方法就足够了)。据我所知，在 PostgreSQL 中没有自动更新属性，我们需要使用触发器来实现。然而，有一个列(`xmin`)出现在所有的表中(尽管我们没有用简单的`select *`看到它)，它会在每次行改变时自动更新，所以我们可以用它来检查并发访问。

Npgsql 中有一个 helper 方法，`ForNpgsqlUseXminAsConcurrencyToken`，它将在一行中配置我在上面所做的一切，但有一点需要注意，它将使用一个[影子属性](https://docs.microsoft.com/en-us/ef/core/modeling/shadow-properties)——映射到数据库并由 EF 模型使用，但没有映射到实体类中，因此对调用代码不可见——相反，在这种情况下我们不希望这样。

使用影子属性，我们只能在一个请求的范围内检测并发问题，这很好，因为它可以避免对事务的需求，但是我们可能想更进一步。通过使用一个普通的属性，并让它一直流向前端，当我们稍后收到对该实体的更新请求时，我们可以检查自客户端最初读取值以来该实体是否发生了更改。如果我们希望避免多个用户同时编辑同一个实体，确保他们在进行更改之前看到最新的值，这可能是有用的。

其余的代码，即使用数据层的业务层，可以保持不变，我们只需要将`RowVersion`属性添加到所有的`Group`表示中，将它映射到层转换(从数据库到服务，然后到视图模型)中，并将其添加到编辑视图中，因此它包含在更新请求中(因此，将`<input asp-for="RowVersion" type="hidden" />`添加到`Details.cshtml`)。

例如，现在如果我们在两个选项卡中打开相同的组详细信息，更新一个，然后尝试更新另一个，我们会“收到”一个错误。

关于这个主题的最后一点，关于更新代码。因为`UpdateAsync`附加了来自前端的完整实体，所以它按预期工作。另一方面，如果我们的更新代码首先从数据库获取实体，然后更新值，我们将需要做一些事情，至少对我来说这不是显而易见的。想象一下`UpdateAsync`的代码如下:

```
public async Task<Group> UpdateAsync(Group group, CancellationToken ct)
{
    var entityToUpdate = await _context.Groups.SingleAsync(g => g.Id == group.Id, ct);
    entityToUpdate.Name = group.Name;
    entityToUpdate.RowVersion = group.RowVersion;
    await _context.SaveChangesAsync(ct);
    return entityToUpdate.ToService();
} 
```

Enter fullscreen mode Exit fullscreen mode

上面给出的代码将只检测获取实体和更新实体之间的并发问题，而不考虑伴随请求而来的`RowVersion`。为了让它被考虑，我们需要做如下的事情:

```
public async Task<Group> UpdateAsync(Group group, CancellationToken ct)
{
    // ...
    _context.Entry(entityToUpdate).OriginalValues.SetValues(new Dictionary<string, object> { { "RowVersion", group.RowVersion} });
    await _context.SaveChangesAsync(ct);
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这不是最好的方法，但它说明了问题。用于并发检查的值是作为原始值保存在 EF 的变更跟踪器中的值，而不是可通过实体访问的值。因此，如果我们希望更新代码首先获取实体，我们需要直接进行这种更改。在最初的实现中，我们没有这个问题，因为我们附加了对象，所以原始值是我们提供的值。

## 其他

和往常一样，在这个系列中，还有很多比我们在这篇文章中谈到的更多的东西，但这对于开始来说已经足够了(我可能会继续做比需要的更复杂的事情)。我们将在以后的章节中回到实体框架核心，因为我们肯定需要向我们的数据库添加更多的东西，以便应用程序变得有用

帖子中的链接:

*   史蒂夫·史密斯对[“024:我需要一个存储库吗？”](http://www.weeklydevtips.com/024)
*   [实体框架核心](https://docs.microsoft.com/en-us/ef/core/)
    *   [迁移](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/)
    *   [设计时数据库上下文创建](https://docs.microsoft.com/en-us/ef/core/miscellaneous/cli/dbcontext-creation)
    *   [并发令牌](https://docs.microsoft.com/en-us/ef/core/modeling/concurrency)
    *   [阴影属性](https://docs.microsoft.com/en-us/ef/core/modeling/shadow-properties)

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode011)。

欢迎并感谢任何问题和反馈。

谢谢你的来访，西阿兹！