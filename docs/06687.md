# 用 ASP.NET 核心构建一个 GraphQL API

> 原文：<https://dev.to/oktadev/build-a-graphql-api-with-asp-net-core-2cfj>

> “没有一艘护卫舰像一本书一样把我们的土地带走……”——艾米莉·狄金森

如今，人们在社交媒体上阅读的内容比在书本上多。也许补救的办法是让阅读更社会化。为此，我们将为书籍创建一个 API。这将是一个使用 GraphQL 的 ASP.NET 核心 2.2 Web API，它将允许开发人员创建安全使用该 API 的应用程序。ASP.NET 核心 2.2 提供了在任何平台上托管 API 的能力，GraphQL 使 API 的消费者可以轻松地查询 API 并以他们想要的方式接收数据。

对于本教程，您所需要的只是 2.2 版本的[。网芯 SDK](https://www.microsoft.com/net/download) 。

这个演示将使用 VS 代码，但是您可以随意使用您喜欢的编辑器或 IDE。

让我们开始吧！

您将创建一个 API，负责在 Okta 服务的帮助下验证令牌。我们的测试客户端将是一个简单的控制台应用程序，它将访问 API。在您的根项目文件夹中创建两个文件夹:`Api`和`Client`。

## 为您的 GraphQL 架构创建您的 ASP.NET 核心 API

在您的`api`文件夹中运行以下命令:

```
dotnet new webapi 
```

因为您将只在本地使用您的应用程序，所以您可以禁用 HTTPS 重定向。打开您的`Startup.cs`文件并找到`Configure`方法。将这个方法的内容更新成这样:

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
  if (env.IsDevelopment())
  {
    app.UseDeveloperExceptionPage();
  }
  app.UseMvc();
} 
```

### 创建您的 ASP.NET 核心 API 模型

在您的`Api`项目中，让我们创建一个名为`Database`的新文件夹。在那个文件夹里面制作一个类`Book.cs` :

```
namespace Api.Database
{
  public class Book
  {
    public string Id { get; set; }

    public string Name { get; set; }

    public bool Published { get; set; }

    public string Genre { get; set; }

    public string AuthorId { get; set; }

    public Author Author { get; set; }
  }
} 
```

在`Database`文件夹中创建另一个类`Author.cs`，内容如下:

```
using System.Collections.Generic;

namespace Api.Database
{
  public class Author
  {
    public int Id { get; set; }
    public string Name { get; set; }
    public List<Book> Books { get; set; }
  }
} 
```

### 设置你的数据库

现在您需要建立与数据库的连接。对于本教程，您将使用带有实体框架核心的 InMemory 数据库。

在你的`Database`文件夹中创建一个新文件`ApplicationDbContext.cs`，包含以下内容:

```
using Microsoft.EntityFrameworkCore;

namespace Api.Database {

public class ApplicationDbContext : DbContext
{
  public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
      { }

    public DbSet<Book> Books { get; set; }
    public DbSet<Author> Authors { get; set; }
  }
} 
```

是时候向应用程序添加 DbContext 了。在`Startup`类内部，找到`ConfigureServices`方法，在开头添加以下内容:

```
services.AddDbContext<ApplicationDbContext>(context =>
{
    context.UseInMemoryDatabase("OktaGraphQL");
}); 
```

确保在`Startup.cs`文件的顶部添加了必要的`using`指令:

```
using Microsoft.EntityFrameworkCore;
using Api.Database; 
```

上面这段代码告诉实体框架使用名为`OktaGraphQL`的内存数据库。这种类型的数据库通常用于测试，您不应该在生产中使用它。然而，这应该足够满足本教程的需求了。

### 种子数据库

由于您使用的是内存数据库，每次新启动应用程序时，数据都会丢失。因此，您可以在应用程序启动时播种数据库。用以下代码更新`Program.cs`文件的内容:

```
using Api.Database;

using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;

namespace Api
{
  public class Program
  {
    public static void Main(string[] args)
    {
      IWebHost host = CreateWebHostBuilder(args).Build();
      using (IServiceScope scope = host.Services.CreateScope())
      {
        ApplicationDbContext context = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();

        var authorDbEntry = context.Authors.Add(
          new Author
          {
            Name = "First Author",
          }
        );

        context.SaveChanges();

        context.Books.AddRange(
          new Book
          {
            Name = "First Book",
            Published = true,
            AuthorId = authorDbEntry.Entity.Id,
            Genre = "Mystery"
          },
          new Book
          {
            Name = "Second Book",
            Published = true,
            AuthorId = authorDbEntry.Entity.Id,
            Genre = "Crime"
          }
        );
      }
      host.Run();
  }

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .UseStartup<Startup>();
  }
} 
```

## 在您的 ASP.NET 核心 API 中设置 GraphQL

现在可以开始添加 GraphQL 相关代码了。您将首先设置一些中间件，使测试您的 GraphQL 端点变得容易得多。

### 图形 QL 中间件

GraphQL 是一个用于探索 graph QL 的浏览器内 IDE。GraphiQL 的特性包括:在字段、参数、类型等前面突出显示智能类型的语法。实时错误突出显示和报告。自动查询完成。运行并检查查询结果。

GraphiQL.NET 允许您通过中间件将 GraphQL 浏览器内编辑器直接包含到您的 ASP.NET 核心应用程序中，让您可以轻松地探索和测试您的 graph QL 端点，从而避免您需要任何额外的依赖。确保您在您的`Api`文件夹中，并运行以下命令:

```
dotnet add package GraphQL --version 2.4.0
dotnet add package graphiql --version 1.2.0 
```

一旦完成，您就可以通过将`app.UseGraphiQl()`中间件添加到您的`Startup.cs`文件中的`Configure`方法来将 GraphiQL.NET 添加到您的 ASP.NET 核心应用程序中。为 GraphiQL 添加`using`指令。净:

```
using GraphiQl; 
```

然后在`app.UseMvc();`之前添加下面一行:

```
app.UseGraphiQl("/graphql"); 
```

这段代码将确保 GraphiQL.NET 接口在`/graphql`端点上运行。

### 图形 SQL 愿望

因为您想要获得关于书籍和作者的数据，所以需要查询`Book`和`Author`类。然而，您不能直接对这些类使用 GraphQL 查询。为了使`Book`类 GraphQL 可查询，您应该创建一个新类型并从泛型类型`ObjectGraphType<T>`扩展它，并且您将传递`Book`类型作为它的泛型参数。

在你的`Api`文件夹中创建一个名为`GraphQL`的新文件夹。现在你可以在`GraphQL`文件夹中创建一个新的类`BookType.cs`。将以下内容粘贴到该文件中:

```
using Api.Database;
using GraphQL.Types;

namespace Api.GraphQL
{
  public class BookType : ObjectGraphType<Book>
  {
    public BookType()
    {
      Name = "Book";

      Field(x => x.Id, type: typeof(IdGraphType)).Description("The ID of the Book.");
      Field(x => x.Name).Description("The name of the Book");
      Field(x => x.Genre).Description("Book genre");
      Field(x => x.Published).Description("If the book is published or not");
    }
  }
} 
```

在同一个文件夹中创建另一个类`AuthorType`，并粘贴以下内容:

```
using Api.Database;
using GraphQL.Types;

namespace Api.GraphQL
{
  public class AuthorType : ObjectGraphType<Author>
  {
    public AuthorType()
    {
      Name = "Author";

      Field(x => x.Id, type: typeof(IdGraphType)).Description("Author's ID.");
      Field(x => x.Name).Description("The name of the Author");
      Field(x => x.Books, type: typeof(ListGraphType<BookType>)).Description("Author's books");
    }
  }
} 
```

现在您可以编写 GraphQL 查询来处理获取作者和作者列表。创建一个名为`AuthorQuery.cs`的新文件，并粘贴以下内容:

```
using System.Linq;
using Api.Database;
using GraphQL.Types;
using Microsoft.EntityFrameworkCore;

namespace Api.GraphQL
{
  public class AuthorQuery : ObjectGraphType
  {
    public AuthorQuery(ApplicationDbContext db)
    {
      Field<AuthorType>(
        "Author",
        arguments: new QueryArguments(
          new QueryArgument<IdGraphType> { Name = "id", Description = "The ID of the Author." }),
        resolve: context =>
        {
          var id = context.GetArgument<int>("id");
          var author = db
            .Authors
            .Include(a => a.Books)
            .FirstOrDefault(i => i.Id == id);
          return author;
        });

      Field<ListGraphType<AuthorType>>(
        "Authors",
        resolve: context =>
        {
          var authors = db.Authors.Include(a => a.Books);
          return authors;
        });
    }
  }
} 
```

客户端将总是发送`POST`请求，该请求将包含查询名称、操作名称和变量。您可以创建一个新的类，作为来自客户端的所有查询的模型。在`GraphQL`文件夹中创建一个名为`GraphQLQuery.cs`的新文件，并粘贴以下代码:

```
using Newtonsoft.Json.Linq;

namespace Api.GraphQL
{
  public class GraphQLQuery
  {
    public string OperationName { get; set; }
    public string NamedQuery { get; set; }
    public string Query { get; set; }
    public JObject Variables { get; set; }
  }
} 
```

### 图形 SQL 控制器

您现在可以创建一个 GraphQL 控制器，它将负责来自您的客户机的所有 GraphQL 查询。在同一个`GraphQL`文件夹中，创建一个名为`GraphQLController.cs`的新文件。现在你可以在新的控制器中使用下面的代码:

```
using System.Threading.Tasks;
using Api.Database;
using GraphQL;
using GraphQL.Types;
using Microsoft.AspNetCore.Mvc;

namespace Api.GraphQL
{
  [Route("graphql")]
  [ApiController]
  public class GraphQLController : Controller
  {
    private readonly ApplicationDbContext _db;

    public GraphQLController(ApplicationDbContext db) => _db = db;

    public async Task<IActionResult> Post([FromBody] GraphQLQuery query)
    {
      var inputs = query.Variables.ToInputs();

      var schema = new Schema
      {
        Query = new AuthorQuery(_db)
      };

      var result = await new DocumentExecuter().ExecuteAsync(_ =>
      {
        _.Schema = schema;
        _.Query = query.Query;
        _.OperationName = query.OperationName;
        _.Inputs = inputs;
      });

      if(result.Errors?.Count > 0)
      {
        return BadRequest();
      }

      return Ok(result);
      }
  }
}

## Secure Your ASP.NET Core GraphQL Application
Sign up for a [forever-free developer account](https://developer.okta.com/signup/) (or log in if you already have one).

<img src="/assets-jekyll/blog/graphql-aspnetcore/okta-signup-accab135cb5e7cb06a3446679d6aef0958ea31b3b9444d87ffb2f70e5882d045.png" alt="Okta Signup" width="800">{: .center-image }

After you have completed your login (and registration), you should see the Dashboard, and in the upper right corner, there should be your unique Org URL. Save it for later.

<img src="/assets-jekyll/blog/graphql-aspnetcore/okta-org-url-0d6947fb477749b48543adb9a99d479b37c77f3229ae27910cc01616a5888d9b.png" alt="Okta Org URL" width="800">{: .center-image }

Now you need to create a new application by browsing to the Applications tab. Click `Add Application`, and from the first page of the wizard choose **Service**.

<img src="/assets-jekyll/blog/graphql-aspnetcore/okta-select-app-10d1fba638111a2d461957ea108d1493ead4aaddb25fbf58522a6c37fe5e1957.png" alt="Okta Select App" width="800">{: .center-image }

On the settings page, enter the name of your application:

<img src="/assets-jekyll/blog/graphql-aspnetcore/okta-app-settings-4b3d46b91f9cb78ac01af8a629634cb1cea271943a5df01fff6476eddc2e0722.png" alt="Okta App Settings" width="800">{: .center-image }

You can now click **Done**.

Now that your application has been created copy down the Client ID and Client secret values on the following page, you'll need them soon (of course, yours will be different).

<img src="/assets-jekyll/blog/graphql-aspnetcore/okta-client-credentials-fc61b85256ab539cc2ae02a699b7397e8147843458538dcce09d1270b24eb5b2.png" alt="Okta Client Credentials" width="800">{: .center-image }

## Validate Your Access Tokens
Since ASP.NET Core comes with enough JWT helpers to help us validate JWT tokens, it will be quite easy to finish this step.

Inside of your `Startup` class add the following to the `ConfigureServices()` method: 
```

cs
服务。add authentic ation(options =>
{
选项。default scheme = JwtBearerDefaults。AuthenticationScheme
})
。AddJwtBearer(options =>
{
选项)。authority = " https://{ youroktad omain }/oauth 2/default "；
选项。audience = " API://default "；
选项。requirementhttps metadata = false；
})；

```
 Make sure to add the `using` directive for the `JwtBearer` namespace to your `Startup` class as well. 
```

cs
使用微软。AspNetCore . authentic ation . jwtbearer；

```
 You will also need a call to ASP.NET Core’s authentication middleware. This middleware should be called before we call the MVC middleware. Place the following above `app.UseGraphiQl("/graphql");` line: 
```

cs
app。use authentication()；

```
 The last thing in the API is to protect the API endpoint. Add the `Authorize` attribute to the `GraphQLController` class. 
```

cs
【授权】
公共异步任务发布([FromBody] GraphQLQuery 查询)
{
//...为了简洁起见，该课程的其余部分被省略

```
 ## Test Your ASP.NET Core GraphQL API

Inside of your `Client` folder run the following: `dotnet new console.`

You can now add the required NuGet packages for your console application: 
```

sh
dotnet 添加包 Newtonsoft。Json -版本 12.0.1

```
 First, you will need to get your sensitive details, that will be used to get a token that grants you access to the protected endpoint on your API. You will need the following details: `Client ID`, `Client secret`, and your `Okta Domain`.

You can now create a new class that will store these details. In your `Client` folder, create a class named `OktaConfig.cs` and paste the following code: 
```

cs
命名空间客户端
{
公共类 OktaConfig
{
公共字符串 TokenUrl { get 设置；}
公共字符串 ClientId { get 设置；}
公共字符串 ClientSecret { get 设置；}
}
}

```
 The `TokenUrl` property is the URL to your default Authorization Server. You can find this in Okta by going to the dashboard and hovering over the **API** menu item in the menu bar, then choosing **Authorization Servers** from the drop-down menu and using the **Issuer URI** for the “default” server. The `ClientId` and `ClientSecret` properties are from the General Settings tab of your API application in Okta.

The `TokenService` will fetch a new access token when needed and reuse the existing one when possible.

You will need an `OktaToken` class to store the token result from Okta services. Create a file called `TokenService.cs` in the `Client` folder. Paste the code below in the class: 
```

cs
使用系统；
使用 Newtonsoft。Json

内部类 ok tatoken
{
[JSON property(property name = " access _ token ")]
公共字符串 AccessToken { get 设置；}

```
 [JsonProperty(PropertyName = "expires_in")]
 public int ExpiresIn { get; set; }

 public DateTime ExpiresAt { get; set; }

 public string Scope { get; set; }

 [JsonProperty(PropertyName = "token_type")]
 public string TokenType { get; set; }

 public bool IsValidAndNotExpiring
 {
     get
     {
         return !String.IsNullOrEmpty(this.AccessToken) &&
   this.ExpiresAt > DateTime.UtcNow.AddSeconds(30);
     }
 } 
```

}

```
 These are all the properties that the token will provide. The only extra thing is the `IsValidAndNotExpiring` property. This just checks the AccessToken is there and isn’t expiring in the next thirty seconds. This way the app can get a new token if it is expiring soon. Now you’ll need a service to use this `OktaToken`.

Create a new file called `ITokenService.cs` and place this interface code inside of the file: 
```

cs
使用系统。线程。任务；

公共接口 ITokenService
{
Task GetToken()；
}

```
 The implementation will decide whether or not to get a new access token or return the one that it has previously received. For the implementation of the interface create the following code in a `TokenService.cs` file in your `Client` folder: 
```

cs
使用系统；
使用系统。集合。泛型；
使用系统。Net . Http
使用系统。Net . Http.Headers
使用系统。文本；
使用系统。线程。任务；
使用 Newtonsoft。Json

命名空间客户端
{
公共类 token service:ITokenService
{
私有 OktaToken _ token = new OktaToken()；
私有只读 OktaConfig _ oktaSettings

```
public TokenService(OktaConfig oktaSettings) => _oktaSettings = oktaSettings;

public async Task<string> GetToken()
{
  if (_token.IsValidAndNotExpiring)
  {
    return _token.AccessToken;
  }

  _token = await GetNewAccessToken();

  return _token.AccessToken;
}

private async Task<OktaToken> GetNewAccessToken()
{
  var client = new HttpClient();
  var clientId = _oktaSettings.ClientId;
  var clientSecret = _oktaSettings.ClientSecret;
  var clientCreds = Encoding.UTF8.GetBytes($"{clientId}:{clientSecret}");

  client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", Convert.ToBase64String(clientCreds));

  var postMessage = new Dictionary<string, string>
        {
            {"grant_type", "client_credentials"},
            {"scope", "access_token"}
        };

  var request = new HttpRequestMessage(HttpMethod.Post, _oktaSettings.TokenUrl)
  {
    Content = new FormUrlEncodedContent(postMessage)
  };

  var response = await client.SendAsync(request);
  if (response.IsSuccessStatusCode)
  {
    var json = await response.Content.ReadAsStringAsync();
    var newToken = JsonConvert.DeserializeObject<OktaToken>(json);
    newToken.ExpiresAt = DateTime.UtcNow.AddSeconds(_token.ExpiresIn);

    return newToken;
  }

  throw new ApplicationException("Unable to retrieve access token from Okta");
} 
```

}
}

```
 This token service starts by getting the OktaConfig that is provided by whoever needs the service. The GetToken() method then checks that `IsValidAndNotExpiring` property and either gets new access token or returns the current one.

The `GetNewAccessToken()` method sets up the `HttpClient` to make the call to the Authorization Server. The `clientCreds` value gets the bytes of a string that has the client ID and secret concatenated with a colon between them. That value is then base64 encoded when it’s added to the `Authorization` header with “Basic “ in front of it.

There are also two key-value pairs sent as `FormUrlEncodedContent`:

- `grant_type`: client\_credentials
- `scope`: access\_token

This tells the Authorization Server that you are sending client credentials and you want to get an access token in exchange.

### Test the Secure ASP.NET Core GraphQL API

Inside of your `Program.cs` file clean up the Main method and make sure it’s empty. At the start of `Main` method add the following piece of code: 
```

cs【var 八进制配置=新八进制配置】

客户端 id = " {八进制客户端 Id}，
客户端代码= " {八进制客户端代码} "，
令牌 URL = " https://{八进制域}/oath 2/default/v1/token "【T6 } }；

```
 This variable will hold all the details Okta needs to authenticate you as a valid user.

With this piece of data, you can now get a token from your `TokenService`. Paste the following code at the end of your `Main` method: 
```

cs
var tokenService =新 tokenService(八进制配置)；
var token = await tokenService。gettoken()；

```
 Since you are using `await` keyword you need to make the method `async`. Change the signature of your `Main` method to look like this: 
```

cs
静态异步任务 Main(string[] args)

```
 You’ll also need to add the `using` directive for the `Task`. 
```

cs
使用系统。线程。任务；

```
 With the token available you can now create a new instance of `HttpClient` and attach the token to the request headers. Paste the following code at the end of the `Main` method: 
```

cs
var http client = new http client()；
httpClient。DefaultRequestHeaders . Authorization = new AuthenticationHeaderValue(" Bearer "，token)；

```
 You can now create the GraphQL query. Append the following piece of code at the end of your `Main` method: 
```

cs
var query = @"
{
作者(id: 1) {
姓名
}
} "；

var post data = new { Query = Query }；
var string content = new string content(JSON convert。SerializeObject(postData)，编码。UTF8，" application/JSON ")；

```
 This code will create a new `StringContent`, with our GraphQL query being part of dynamic object `{ Query = query}`.

After that is in place, you can issue a request to the API. Add the following code at the end of the `Main` method: 
```

cs
var RES = await http client。PostAsync(postUri，string content)；
if(RES . IsSuccessStatusCode)
{
var content = await RES . content . readasstringasync()；

控制台。WriteLine(内容)；
}
其他
{
控制台。出现 WriteLine($ "错误...状态代码:{ RES . status code } "；
}

```
 ## Run Your ASP.NET Core GraphQL Application

Open two terminals: one inside of your `Client` and one in your `Api` folders. Inside of the `Api` folder enter the following bash command: 
```

sh
dotnet run

```
 This will start the API application and make sure our application is listening for any incoming requests.

It’s time to test your console application. Inside of your `Client` folder run the same command: 
```

sh
dotnet run

```
 If everything worked as expected, you should get the following output inside of your terminal: 
```

sh
{ "数据":{ "作者":{ "姓名":"第一作者" }}

```
 To test the security, just comment out the line: 
```

cs
httpClient。DefaultRequestHeaders . Authorization = new AuthenticationHeaderValue(" Bearer "，token)；

```
 This will stop sending the bearer token in the headers and you should get an `Unauthorized` error.

Now you have an API that is secure and lets you control who has access to it!

## Learn More About ASP.NET Core, GraphQL, and Secure User Management

Here are some other great resources to check:

- [Token Authentication in ASP.NET Core 2.0 - A Complete Guide](https://developer.okta.com/blog/2018/03/23/token-authentication-aspnetcore-complete-guide)
- [Build a Simple CRUD App with ASP.NET Core and Vue](https://developer.okta.com/blog/2018/08/27/build-crud-app-vuejs-netcore)
- [Build a Secure CRUD App with ASP.NET Core and React](https://developer.okta.com/blog/2018/07/02/build-a-secure-crud-app-with-aspnetcore-and-react)
- [Build a Simple API Service with Express and GraphQL](https://developer.okta.com/blog/2018/09/27/build-a-simple-api-service-with-express-and-graphql)
- [Build a Secure API with Spring Boot and GraphQL](https://developer.okta.com/blog/2018/08/16/secure-api-spring-boot-graphql)

And as always, we’d love to hear from you. Hit us up with questions or feedback in the comments, or on Twitter [@oktadev](https://twitter.com/oktadev). 
```