# 使用 NSwag 生成用于 HttpClientFactory 的类型化客户端

> 原文：<https://dev.to/stuartblang/generating-a-typed-client-for-use-with-httpclientfactory-using-nswag-3e86>

如果你是一名构建 web 应用或微服务的. NET 开发人员，你可能会想从 ASP.NET 核心应用中调用 HTTP API。这篇文章讲述了如何使用 NSwag 创建一个客户端，以及使用它和`HttpClientFactory`的适当设置。

## 键入客户端和`HttpClientFactory`

对于这些概念的正确介绍，我鼓励您阅读 [Steve Gordon](https://twitter.com/stevejgordon) 的 ASP.NET 核心 2.1 系列中的 HttpClientFactory、[HttpClientFactory 简介](https://www.stevejgordon.co.uk/introduction-to-httpclientfactory-aspnetcore)和[定义命名和类型化客户端](https://www.stevejgordon.co.uk/httpclientfactory-named-typed-clients-aspnetcore)，但真正简短的版本是:

*   `HttpClientFactory` -这是为了解决管理`HttpClient`及其处理程序的生命周期的问题而引入的，这在历史上一直是个问题。
*   类型化客户端——这是`HttpClientFactory`的一个特性，它允许你注册一个简单包装`HttpClient`的类(即在其构造函数中接受一个),从而给你的 API 一个类型化客户端，同时不拥有任何`HttpClient`的生命周期，让`HttpClientFactory`做它的事情。

## NSwag

在这方面 [NSwag](http://nswag.org) 适合做什么？

一方面，NSwag 类似于 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) ，只需几行代码，你就可以让你的 ASP.NET 核心应用在运行时在你的应用内提供一个 swagger 文档和 swagger ui。然后它还有 [AutoRest](https://github.com/Azure/autorest) 的功能，它可以取一个 swagger 文件，为你生成一个 C#类型化的客户端(还有其他语言比如 TypeScript)。

关于 NSwag，有几件事让我印象深刻:

*   积分数——简直是瑞士军刀！只需花一分钟时间阅读 [README](https://github.com/RSuter/NSwag/blob/master/README.md) ，您很快就会明白，它不仅可以在运行时生成 swagger 或 OpenAPI 文件，还可以在编译时从您的 web api 程序集生成。然后，您可以生成各种语言的客户端，并进行大量设置。
*   Rico Suter 是主要的维护者，他一直在为此做出贡献，对问题反应迅速，并检查修复和功能。就好像他有一个团队在管理他的 GitHub 账户！

## 以 swagger.json 开头

我们将从`swagger.json`生成一个客户端，如果您需要从您自己的 ASP.NET 核心应用程序生成这个客户端，那么您可以使用 NSwag CLI 生成一个`swagger.json`，[参见此处](https://github.com/RSuter/NSwag/wiki/AspNetCoreToSwaggerCommand)。

## 生成构建中的客户端

为此，我们将使用`NSwag.ConsoleCore` CLI 工具包(我们也可以使用 [NSwag。MSBuild](https://www.nuget.org/packages/NSwag.MSBuild/) 包，流程大体相同)。我正在使用[宠物店 swagger](https://petstore.swagger.io/#/) ，并从将`swagger.json`放入我的项目文件夹开始。

有两种方法可以将 config 传递到 NSwag 命令中，一种是通过命令行参数，另一种是通过 JSON 配置文件，我将在这里使用这个文件。

首先创建一个空库(这里我们将目标定为`netstandard2.0`)并将下面的代码片段添加到您的`.csproj`文件中的`ItemGroup`:

```
<DotNetCliToolReference Include="NSwag.ConsoleCore" Version="12.0.15" /> 
```

在一个`dotnet restore`之后，我们现在可以在项目文件夹`dotnet nswag new`中运行，这将为我们创建一个默认的配置文件，我们可以将其剥离，这样[看起来就像这个](https://github.com/slang25/nswag-petstore-client/blob/master/NSwag.PetStore.Client/nswag.json)。这里有一个精简版:

```
{  "runtime":  "NetCore22",  "defaultVariables":  null,  "swaggerGenerator":{  "fromSwagger":{  "json":"$(InputSwagger)"  }  },  "codeGenerators":  {  "swaggerToCSharpClient":  {  ...  OMITTING  LOTS  OF  DEFAULT  CONFIG  ...  "generateClientInterfaces":  true,  "injectHttpClient":  true,  "disposeHttpClient":  false,  "generateExceptionClasses":  true,  "exceptionClass":  "$(ClientName)Exception",  "useBaseUrl":  false,  "className":  "$(ClientName)Client",  "generateOptionalParameters":  true,  "generateJsonMethods":  false,  "namespace":  "$(ClientNamespace)",  "classStyle":  "Poco",  "output":  "$(GeneratedSwaggerClientFile)"  }  }  } 
```

以下是我更改的默认设置:

*   这为我们的类型化客户端提供了一个接口，抽象出了实现。这可能看起来像是你不会想到不做的事情，但是在测试中使用类型化客户端的实现和在`HttpClient`级别嘲笑是有好处的(大声喊到 [httpclient-interception](https://github.com/justeat/httpclient-interception) )。
*   `InjectHttpClient` -这为客户端创建了一个构造函数，它接受一个`HttpClient`，这是我们使用它作为带有`HttpClientFactory`的类型化客户端所需要的。
*   `disposeHttpClient` -当与`HttpClientFactory`一起使用时，如果你在`HttpClient`上调用`Dispose`基本上没关系，它基本上是不可操作的。
*   `generateOptionalParameters` -默认情况下，NSwag 将为每个操作创建 2 个方法，一个有一个没有`CancellationToken`，启用此选项将会合并它们。
*   默认情况下，NSwag 将添加序列化和反序列化类型的实例方法，它们只是委托给 Json.NET 的单行静态方法。我喜欢我的 POCOs 漂亮和简单，所以我禁用这个。
*   `useBaseUrl` -这很重要，我们只想使用`HttpClient`的`BaseAddress`，我们不想用类型化客户端上的属性覆盖它(这是默认情况下会发生的)，所以我们将它设置为`false`。我们可以在注册类型化客户端的时候配置`BaseAddress`。
*   `exceptionClass` -出错时抛出的异常类型，默认情况下是`SwaggerException`，感觉像是实现泄漏。
*   默认情况下，NSwag 使用样式`Inpc`，这是`INotifyPropertyChange`的缩写，这是一个奇怪的默认，我只能认为如果你正在构建一个 MVVM 客户端应用程序，并且想要重用直接绑定的类，它会很有用(我仍然觉得不对)。通过选择`Poco`，我们得到了一个具有 getter 和 setter 属性的典型类。

你可以看到我在这个文件中使用了变量，比如`"$(InputSwagger)"`，我们可以通过`nswag`命令传递这些变量。

我们想要做的只是做一个`dotnet build`并让项目自动创建生成的`.cs`文件，并将它包含在要构建的文件中。所以这里有`.csproj` :

```
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
        <DebugType>embedded</DebugType>
        <EmbedAllSources>true</EmbedAllSources>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Newtonsoft.Json" Version="12.0.1" />
        <PackageReference Include="System.ComponentModel.Annotations" Version="4.5.0" />
        <DotNetCliToolReference Include="NSwag.ConsoleCore" Version="12.0.15" />
    </ItemGroup>

    <Target Name="GenerateNSwagClient">
        <PropertyGroup>
            <InputSwagger>swagger.json</InputSwagger>
            <ClientName>PetStore</ClientName>
            <GeneratedSwaggerClientFile Condition="'$(GeneratedSwaggerClientFile)' ==''">$(IntermediateOutputPath)$(MSBuildProjectName).$(ClientName)Client.cs</GeneratedSwaggerClientFile>
        </PropertyGroup>
        <Exec Command="dotnet nswag run nswag.json /variables:InputSwagger=$(InputSwagger),ClientName=$(ClientName),ClientNamespace=$(RootNamespace),GeneratedSwaggerClientFile=$(GeneratedSwaggerClientFile)" />
    </Target>

    <Target Name="IncludeNSwagClient" BeforeTargets="CoreCompile" DependsOnTargets="GenerateNSwagClient">
        <ItemGroup Condition="Exists('$(GeneratedSwaggerClientFile)')">
            <Compile Include="$(GeneratedSwaggerClientFile)" />
            <FileWrites Include="$(GeneratedSwaggerClientFile)" />
        </ItemGroup>
    </Target>
</Project> 
```

需要注意的事项:

*   这个文件中唯一一个专门针对宠物店 swagger 的是`<ClientName>PetStore</ClientName>`，你可以随意把它作为你自己客户的食谱。
*   我们在`CoreCompile`之前挂钩我们的目标，给我们机会添加要编译的文件。
*   生成的文件进入`IntermediateOutputPath`，这是`obj`中特定于配置的文件夹，这正是`AssemblyInfo.cs`的生成方式，并且很可能已经是`.gitignore`的一部分。
*   我们已经包含了必需的`Newtonsoft.Json`和`System.ComponentModel.Annotations`包。
*   使用`variables`参数将变量传递给`nswag.json`。
*   `<DebugType>embedded</DebugType>`和`<EmbedAllSources>true</EmbedAllSources>`给了我们一个`.dll`，它包含了一个嵌入式 PDB 文件，其中包含了所有用于调试目的的源代码。你也可以使用[符号包](https://docs.microsoft.com/en-us/nuget/create-packages/symbol-packages-snupkg)，在我看来这是这个解决方案的倒退。

## 使用客户端配合`HttpClientFactory`

对，我们有我们的包/项目，让我们使用它！

让我们使用模板创建一个新的 ASP.NET 核心 Web API，添加对我们的客户端项目的引用，并添加`Microsoft.Extensions.Http`包。[请看这里](https://github.com/slang25/nswag-petstore-client/blob/master/SampleApp/SampleApp.csproj)的例子。

现在我们可以在`ConfigureServices`方法中注册我们的客户端，就像这样:

```
services.AddHttpClient<IPetStoreClient, PetStoreClient>(c => c.BaseAddress = new Uri("https://petstore.swagger.io/v2/")); 
```

就是这样！现在我们可以开始使用我们的客户端了，这里有一个来自我们控制器的例子:

```
[Route("api/[controller]")]
[ApiController]
public class ValuesController : ControllerBase
{
    readonly IPetStoreClient petStoreClient;

    public ValuesController(IPetStoreClient petStoreClient)
    {
        this.petStoreClient = petStoreClient;
    }

    [HttpGet]
    public async Task<ActionResult<IEnumerable<string>>> Get(CancellationToken ct)
    {
        var stu = await petStoreClient.GetUserByNameAsync("Stu", ct);
        return new[] { stu.Email };
    }
} 
```

你可以在这里看到所有的[代码](https://github.com/slang25/nswag-petstore-client)。