# 。NET 核心和集成测试

> 原文：<https://dev.to/lhersey/net-core-and-integration-tests-5h3m>

# 集成测试为。净核心-干净的架构

这个项目有一个如何用 [xUnit](https://xunit.github.io/) 、 [TestServer](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.testhost.testserver?view=aspnetcore-2.1) 、[fluent assessments](https://github.com/JeremySkinner/FluentValidation)等编写干净且可维护的测试的例子！

### 生产代码

首先让我们看看所有的产品代码...(数据库模式配置、哈希、JWT 配置、令牌生成、验证)。

这是文件夹结构:

```
CleanVidly/
    src/
        CleanVidly/
            Controllers/        <-- All the controllers with their DTOS and validators
            Extensions/         <-- Extension methods for pagination and other things
            Core/               <-- Abstractions and Entities
            Mapping/            <-- AutoMapper configuration 
            Persistance/        <-- All repositories and database schema configuration (Migrations here too)
            Infraestructure/    <-- Helpers classes for generate JWT and password hashing
    tests/
        CleanVidly.IntegrationsTest/
            Controllers/        <-- Endpoints tests
            Extensions/         <-- Extensions for testing
            Helpers/            <-- Helpers for testing 
```

Enter fullscreen mode Exit fullscreen mode

一切都会有意义！耐心点！在测试之前，我想和你分享产品代码的部分。

是一个简单的应用程序，结构简洁明了，除了一个用于 JWT 登录的端点外，还有所有用于“类别”和“角色”的 REST 端点。

### 为什么这个解决方案没有拆分成多个项目？(视图/业务逻辑/数据)

关于这一点，请看 Mosh Hamedani 的帖子。

反转依赖原则在实践中即使只有一个项目,**控制器**层依赖于**核心**层的抽象，而**持续性**也依赖于**核心**。所有依赖于依赖方向，没有文件夹，没有项目。

##### JWT 令牌生成和验证与角色验证。

用
在 **Startup.cs** 上设置令牌服务和授权中间件

```
private void GetAuthenticationOptions(AuthenticationOptions options)
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
}

private void GetJwtBearerOptions(JwtBearerOptions options)
{
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = true,
        ValidateAudience = true,
        ValidateLifetime = true,
        ValidateIssuerSigningKey = true,

        ValidIssuer = Configuration["Jwt:ValidIssuer"],
        ValidAudience = Configuration["Jwt:ValidAudience"],
        IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(Configuration["Jwt:SecurityKey"]))
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

在带有
的 **ConfigureServices** 方法中使用它们

```
services
    .AddAuthentication(GetAuthenticationOptions)
    .AddJwtBearer(GetJwtBearerOptions); 
```

Enter fullscreen mode Exit fullscreen mode

并将此添加到**配置**方法
中

```
app.UseAuthentication();
app.UseMvc(); 
```

Enter fullscreen mode Exit fullscreen mode

使用
在每次有效登录时生成令牌

```
public string GenerateToken(User user)
{

    var claims = new List<Claim>() {
        new Claim(JwtRegisteredClaimNames.NameId, user.Id.ToString()),
        new Claim(JwtRegisteredClaimNames.UniqueName, user.Email),
        new Claim("name", user.Name),
        new Claim("lastname", user.Lastname),
        new Claim("joinDate", user.JoinDate.ToString("dd/MM/yyyy H:mm")),
    };

    var roles = user.UserRoles.Select(ur => new Claim("roles", ur.Role.Description));
    claims.AddRange(roles);

    return GetJwtToken(claims);
}

private string GetJwtToken(IEnumerable<Claim> claims)
{
    var secretKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(securityKey));
    var signinCredentials = new SigningCredentials(secretKey, SecurityAlgorithms.HmacSha512);

    var tokeOptions = new JwtSecurityToken(
        issuer: validIssuer,
        audience: validAudience,
        claims: claims,
        expires: DateTime.UtcNow.AddHours(24),
        signingCredentials: signinCredentials
    );

    return new JwtSecurityTokenHandler().WriteToken(tokeOptions);
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 验证

我更喜欢使用 [FluentValidation](https://github.com/JeremySkinner/FluentValidation) 来避免数据丢失，因为它非常强大

```
public class UserValidator : AbstractValidator<SaveUserResource>
{
    public UserValidator()
    {
        RuleFor(u => u.Name).NotEmpty().MinimumLength(4).MaximumLength(32);
        RuleFor(u => u.Lastname).NotEmpty().MinimumLength(4).MaximumLength(32);
        RuleFor(u => u.Email).NotEmpty().MinimumLength(4).MaximumLength(128).EmailAddress();
        RuleFor(u => u.Roles).NotEmpty();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它更干净，并且尊重**单一责任原则！**支持正则表达式、异步验证等。

为了对每个请求自动验证所有 dto，在我们的启动类
中添加一行代码

```
services
    .AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
    .AddFluentValidation(fvc => fvc.RegisterValidatorsFromAssemblyContaining<Startup>()); 
```

Enter fullscreen mode Exit fullscreen mode

这一行告诉搜索当前程序集上的所有验证器。

在每个请求中，如果添加一个 dto 作为一个 enpoint 的参数，它将检查一个验证器，验证它，如果有任何错误，返回一个 400 BadRequest，包含所有错误，对吗？

##### 数据库模式

为了配置所有的列和表，我不使用数据标注，而是使用纯 FluentAPI，但是这个 **OnModelCreating 方法**代码太多，很麻烦...所以我使用 **IEntityTypeConfiguration** 为每个模型创建一个配置类:

```
public class UserConfiguration : IEntityTypeConfiguration<User>
{
    public void Configure(EntityTypeBuilder<User> builder)
    {
        builder.Property(u => u.Email).HasMaxLength(128).IsRequired();
        builder.Property(u => u.Name).HasMaxLength(32).IsRequired();
        builder.Property(u => u.Lastname).HasMaxLength(32).IsRequired();
        builder.Property(u => u.Salt).HasMaxLength(128).IsRequired();
        builder.Property(u => u.Password).HasMaxLength(64).IsRequired();
        builder.Property(u => u.JoinDate).HasDefaultValueSql("GETDATE()").IsRequired();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

并且在 **OnModelCreating** 中调用这个来读取当前程序集上的所有配置类:

```
modelBuilder.ApplyConfigurationsFromAssembly(Assembly.GetExecutingAssembly()); 
```

Enter fullscreen mode Exit fullscreen mode

##### 密码哈希

使用这些函数进行密码散列:

```
public static byte[] CreateHash(byte[] salt, string valueToHash)
{
    using (var hmac = new HMACSHA512(salt))
    {
        return hmac.ComputeHash(Encoding.UTF8.GetBytes(valueToHash));
    }
}

public static byte[] GenerateSalt()
{
    using (var hmac = new HMACSHA512())
    {
        return hmac.Key;
    }
}

public static bool VerifyHash(string password, byte[] salt, byte[] actualPassword)
{
    using (var hmac = new HMACSHA512(salt))
    {
        var computedHash = hmac.ComputeHash(Encoding.UTF8.GetBytes(password));
        return computedHash.SequenceEqual(actualPassword);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 如何设置集成测试？

我准备了一个配置单例实例来读取 **appSettings。测试项目上的 Test.json** ，没有来自源项目的普通 **appSettings** 。

```
private static IConfigurationRoot configuration;
private ConfigurationSingleton() { }
public static IConfigurationRoot GetConfiguration()
{
    if (configuration is null)
        configuration = new ConfigurationBuilder()
        .SetBasePath(Path.Combine(Path.GetFullPath("../../../")))
        .AddJsonFile("appsettings.Test.json")
        .AddEnvironmentVariables()
        .Build();

    return configuration;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个 DbContext 来验证每个端点做了它应该做的事情，所以创建一个 **DbContextFactory:**

```
public DbContextFactory()
{
    var dbBuilder = GetContextBuilderOptions<CleanVidlyDbContext>("vidly_db");

    Context = new CleanVidlyDbContext(dbBuilder.Options);
    Context.Database.Migrate(); //Execute migrations
} 
```

Enter fullscreen mode Exit fullscreen mode

有时，这个上下文并不知道 API 所做的更改，所以我们需要刷新上下文:

```
public CleanVidlyDbContext GetRefreshContext()
{
    var dbBuilder = GetContextBuilderOptions<CleanVidlyDbContext>("vidly_db");
    Context = new CleanVidlyDbContext(dbBuilder.Options);

    return Context;
} 
```

Enter fullscreen mode Exit fullscreen mode

TestServer 创建的客户端有时有点难以使用，所以 **Request** 类有助于使它变得简单一些。

```
public JwtAuthentication Jwt => new JwtAuthentication(ConfigurationSingleton.GetConfiguration());

//Returns this to chain with the REST endpoint ex: `request.AddAuth(token).Get("api/sensitiveData");`
public Request<TStartup> AddAuth(string token)
{
    this.client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    return this;
}

/**
 * Also Get, post, put and delete methods
 */ 
```

Enter fullscreen mode Exit fullscreen mode

还创建了一个扩展方法，用更干净的代码读取响应的主体

```
public static async Task<T> BodyAs<T>(this HttpResponseMessage httpResponseMessage)
{
    var bodyString = await httpResponseMessage.Content.ReadAsStringAsync();
    return JsonConvert.DeserializeObject<T>(bodyString);
} 
```

Enter fullscreen mode Exit fullscreen mode

我就叫

```
res.BodyAs<Employees[]>(); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在测试...

### 如何正确创建集成测试？

我总是将我的测试设置为在‘快乐的道路’上运行，并且对于每个测试，改变我需要改变的！

首先在每个类上，实现这些接口

```
public class CategoriesControllerPostTests : IClassFixture<Request<Startup>>, IClassFixture<DbContextFactory>, IDisposable 
```

Enter fullscreen mode Exit fullscreen mode

构造函数将在类的每个测试之前运行**，IDisposable 将在每个测试**之后运行 Dispose 方法**，**

**IClassFixture** 在测试之间有一个单独的上下文，所以我们使用创建 Request 和 DbcontextFactory 实例，并为它们创建一个构造函数。

```
private readonly Request<Startup> request;
private readonly CleanVidlyDbContext context;
public CategoriesControllerPostTests(Request<Startup> request, DbContextFactory contextFactory)
{
    this.request = request;
    this.context = contextFactory.Context;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要在干净的状态下运行每个测试...Dispose 方法在每次测试后清理数据库。

```
public void Dispose()
{
    context.Categories.RemoveRange(context.Categories);
    context.SaveChanges();
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们编写我们的第一个集成测试！

```
[Fact]
public async Task ShouldSave_Category_IfInputValid()
{
    //Send a request to /api/cateogires with that body
    await request.Post("/api/categories", new { Description = "My description" });

    //Select directly to the Db;
    var categoryInDb = await context.Categories.FirstOrDefaultAsync(c => c.Description == "My description");

    //Validate that is not null (Thanks FluentAssertions!)
    categoryInDb.Should().NotBeNull();
} 
```

Enter fullscreen mode Exit fullscreen mode

但是这个实现有一个问题，请求将失败，状态为 401 未授权...所以我们需要做出改变...

```
[Fact]
public async Task ShouldSave_Category_IfInputValid()
{
    //Create user to generate JWT token
    var user = new User()
    {
        Email = "",
        Name = "",
        Lastname = "",
        Id = 1
    };

    //Generate token
    var Token = request.Jwt.GenerateToken(user);

    //Send a request to /api/cateogires with that body and token
    await request.AddAuth(Token)Post("/api/categories", new { Description = "My description" });

    //Select directly to the Db;
    var categoryInDb = await context.Categories.FirstOrDefaultAsync(c => c.Description == "My description");

    //Validate that is not null (Thanks FluentAssertions!)
    categoryInDb.Should().NotBeNull();
} 
```

Enter fullscreen mode Exit fullscreen mode

但是我们的考试越来越难了...我更喜欢不同的方法...让我们做些改变吧！将请求提取到另一个方法中，并将**描述和令牌提取为变量**，以便根据测试来更改它们。
我们得到了这个:

```
private readonly Request<Startup> request;
private readonly CleanVidlyDbContext context;

private string Description;
private string Token;

public CategoriesControllerPostTests(Request<Startup> request, DbContextFactory contextFactory)
{
    this.request = request;
    this.context = contextFactory.Context;

    Description = "Valid Category"; //Initalize with some valid value!

    var user = new User()
    {
        Email = "",
        Name = "",
        Lastname = "",
        Id = 1
    };

    Token = request.Jwt.GenerateToken(user);
}

public void Dispose()
{
    context.Categories.RemoveRange(context.Categories);
    context.SaveChanges();
}

public Task<HttpResponseMessage> Exec() => request.AddAuth(Token).Post("/api/categories", new { Description = Description });

[Fact]
public async Task ShouldSave_Category_IfInputValid()
{
    //Don't change anything, this is the test of the happy path!
    await Exec(); 

    //Should be equal to the variable without change
    var categoryInDb = context.Categories.FirstOrDefault(c => c.Description == Description); 

    //Should found the category
    categoryInDb.Should().NotBeNull();
} 
```

Enter fullscreen mode Exit fullscreen mode

一切都好，但如何测试其他路径？没有提供描述怎么办？它会以 400 BadRequest 回应吗？

```
[Fact]
public async Task ShouldReturn_BadRequest400_IfDescription_LessThanFourCharacters()
{
    Description = "a"; //Just change what the test should supose to test and call Exec(); It is cleaner!

    var res = await Exec();
    var body = await res.BodyAs<ValidationErrorResource>(); //It supose to return a error model with the errors!

    res.StatusCode.Should().Be(HttpStatusCode.BadRequest); 
    body.Errors.Should().ContainKey("Description"); //Should return an error for Description (From FluentValidation!)
} 
```

Enter fullscreen mode Exit fullscreen mode

它简单、干净、不言自明，测试完全按照它说的做，不多也不少！
满班是:

```
private readonly Request<Startup> request;
private readonly ITestOutputHelper output;
private readonly CleanVidlyDbContext context;
private string Description;
private string Token;
public CategoriesControllerPostTests(Request<Startup> request, DbContextFactory contextFactory)
{
    this.request = request;
    this.context = contextFactory.Context;

    //Set before each test, remember... The constructor runs before each test
    Description = "Valid Category";  

    var user = new User()
    {
        Email = "",
        Name = "",
        Lastname = "",
        Id = 1
    };

    Token = request.Jwt.GenerateToken(user);

}

public void Dispose()
{
    context.Categories.RemoveRange(context.Categories);
    context.SaveChanges();
}

public Task<HttpResponseMessage> Exec() => request.AddAuth(Token).Post("/api/categories", new { Description = Description });

[Fact]
public async Task ShouldSave_Category_IfInputValid()
{
    await Exec();
    var categoryInDb = context.Categories.FirstOrDefault(c => c.Description == Description);
    categoryInDb.Should().NotBeNull();
}

[Fact]
public async Task ShouldRetuns_Category_IfInputValid()
{
    var res = await Exec();
    var body = await res.BodyAs<KeyValuePairResource>();

    body.Id.Should().BeGreaterThan(0, "Id should by set by EF");
    body.Description.Should().Be(Description, "Is the same description sended on Exec();");
}

[Fact]
public async Task ShouldReturn_BadRequest400_IfDescription_LessThanFourCharacters()
{
    Description = "a";

    var res = await Exec();
    var body = await res.BodyAs<ValidationErrorResource>();

    res.StatusCode.Should().Be(HttpStatusCode.BadRequest);
    body.Errors.Should().ContainKey("Description");
}

[Fact]
public async Task ShouldReturn_BadRequest400_IfDescription_GreaterThanSixtyFourCharacters()
{
    //Create a string of 65 characters long 
    Description = string.Join("a", new char[66]);

    var res = await Exec();
    var body = await res.BodyAs<ValidationErrorResource>();

    res.StatusCode.Should().Be(HttpStatusCode.BadRequest);
    body.Errors.Should().ContainKey("Description");
}

[Fact]
public async Task ShouldReturn_Unauthorized401_IfNoTokenProvided()
{
    Token = "";

    var res = await Exec();

    res.StatusCode.Should().Be(HttpStatusCode.Unauthorized);
} 
```

Enter fullscreen mode Exit fullscreen mode

这将测试我们类别的 POST 端点的所有执行路径！干得好！

设置和维护集成测试是很难的，我希望我有所帮助！

### 要求

如果克隆存储库，您需要:

*   。NET Core 2.2 预览版(您可以尝试在。csproj 如果不想安装预览版)
*   SQL Server 的一个实例

在 **src/CleanVidly/** 中，使用
设置 SQL Server 连接字符串的用户密码

```
$ dotnet user-secrets set "ConnectionStrings:vidly_db" "Your connection string" 
```

Enter fullscreen mode Exit fullscreen mode

然后运行

```
$ dotnet ef database update 
```

Enter fullscreen mode Exit fullscreen mode

要运行测试，您有两个选择，用您的测试连接字符串设置一个环境变量，或者在 **appSettings 中硬编码。Test.json** 在测试项目上，用户机密只是针对**开发环境。**

如果你想看完整的项目，可以去 Github 仓库

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [海尔赛](https://github.com/lHersey) / [视频整合测试](https://github.com/lHersey/VidlyIntegrationTest)

### 的集成测试的干净实现。网络核心

[View on GitHub](https://github.com/lHersey/VidlyIntegrationTest)

如果你知道更好的方法，请打开一个问题！我想了解一下你的经历！如果你需要帮助，请留下你的评论！

谢谢！