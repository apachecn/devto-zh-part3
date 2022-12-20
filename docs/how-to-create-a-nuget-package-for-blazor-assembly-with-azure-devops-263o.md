# 如何使用 Azure DevOps 为 Blazor 程序集创建 Nuget 包

> 原文：<https://dev.to/rembou1/how-to-create-a-nuget-package-for-blazor-assembly-with-azure-devops-263o>

# 如何用 Azure DevOps 为 Blazor assembly 创建 Nuget 包

几周前，我发现了一种使用 DataAnnotations ( [此处为](https://remibou.github.io/Client-side-validation-with-Blazor-and-Data-Annotations/))在 Blazor 上验证表单的方法。在这篇博文中，我将解释我如何用它创建一个包，并把它作为 nuget 包发布。

## 解

已经有一个创建 Blazor librairy 的模板，所以只需创建一个项目并键入

```
dotnet new blazorlib 
```

这将在当前目录中创建项目 csproj 和第一个文件。对于我的项目，我创建了以下解决方案结构:

*   图书馆本身
*   样品
*   测试项目(目前为空)

这个 blazor 库对于 csproj 的产生非常有趣。我们可以找到以下几行:

```
<!-- .js/.css files will be referenced via <script>/<link> tags; other content files will just be included in the app's 'dist' directory without any tags referencing them -->
    <EmbeddedResource Include="content\**\*.js" LogicalName="blazor:js:%(RecursiveDir)%(Filename)%(Extension)" />
    <EmbeddedResource Include="content\**\*.css" LogicalName="blazor:css:%(RecursiveDir)%(Filename)%(Extension)" />
    <EmbeddedResource Include="content\**" Exclude="**\*.js;**\*.css" LogicalName="blazor:file:%(RecursiveDir)%(Filename)%(Extension)" /> 
```

这意味着 content 文件夹中包含的所有 js 和 css 都将自动加载到客户端应用程序的组件中，这使得重用 js 和 css 变得很容易。

## 包定义

为了定义你的包，你必须在你的 csproj 文件中添加一些信息，这是我在第一个 PropertyGroup 标签中添加的:

```
<Authors>Rémi BOURGAREL</Authors>
<PackageTags>blazor validation dataannotation</PackageTags>
<RepositoryUrl>https://github.com/RemiBou/RemiBou.Blazor.DataAnnotation</RepositoryUrl>
<PackageProjectUrl>https://github.com/RemiBou/RemiBou.Blazor.DataAnnotation</PackageProjectUrl>
<Description>Blazor component for validating your form with DataAnnotations attributes. Made from this blog post https://remibou.github.io/Client-side-validation-with-Blazor-and-Data-Annotations/</Description> 
```

*   这些来自这里[https://docs . Microsoft . com/en-us/dot net/core/tools/csproj # nuget-metadata-properties](https://docs.microsoft.com/en-us/dotnet/core/tools/csproj#nuget-metadata-properties)

## Azure DevOps 并上传到 nuget

对于这一步，您需要在[nuget.org](https://www.nuget.org/)和 [Azure DevOps](https://dev.azure.com/) 上拥有一个帐户。然后你在你的 Azure DevOps 账户的服务连接上注册你的 nuget 连接(见[这里](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=vsts))。

这是我的 azure 管道定义

```
trigger:
- master

pool:
  vmImage: 'vs2017-win2016'

variables:
  buildConfiguration: 'Release'

steps:
- task: DotNetCoreInstaller@0
  displayName: 'Install  SDK'
  inputs:
    version: 2.2.100    
- task: NuGetToolInstaller@0
  inputs:
    versionSpec: '4.9.2' 
- task: DotNetCoreCLI@2
  displayName: 'dotnet  pack'
  inputs:
    command: pack
    packagesToPack: RemiBou.Blazor.DataAnnotation/RemiBou.Blazor.DataAnnotation.csproj
    versioningScheme: byPrereleaseNumber
    configuration: $(buildConfiguration)
    majorVersion: '0' 
    minorVersion: '0' 
    patchVersion: '0'
    verbosityPack: Detailed
- task: NuGetCommand@2
  inputs:
    command: push
    nuGetFeedType: external
    publishFeedCredentials: 'Nuget' 
```

*   我不得不从 Blazor 重新命名我的项目。remi bou . data remi bou 的注释。因为微软保留了前缀“Blazor”(错误“409 此包 ID 已被保留...”).
*   我必须更新 nuget，因为 runner 使用 4.1，而我们需要 4.8 来发送 net 标准包(错误“无法转换类型为‘System’的对象。“字符串”到“类型”的 NuGet。Frameworks.NuGetFramework”)
*   我使用“byPrereleaseNumber ”,所以每次我向主分支提交时，都会创建一个新的版本号作为预发布版本。我稍后会查看这个包是否需要发布。
*   我使用 dotnet pack 是因为 nuget pack 不能很好地读取 csproj 并忽略依赖关系。
*   我使用 nuget push 而不是 dotnet push，因为 dotnet push 不支持 Azure DevOps 中的服务连接(错误“DotNetCore 当前不支持使用加密的 Api 密钥。”)

在推动我的分支并等待几分钟后，我的新软件包版本出现在这里[https://www.nuget.org/packages/RemiBou.Blazor.DataAnnotation](https://www.nuget.org/packages/RemiBou.Blazor.DataAnnotation) 。

## 使用包

在项目文件夹中，你输入 nuget.org 给出的命令

```
dotnet add package RemiBou.Blazor.DataAnnotation --version 0.0.0-CI-20181214-215602 
```

并在项目的 _ViewImport.cshtml 上添加

```
@addTagHelper *, RemiBou.Blazor.DataAnnotation 
```

所以现在你可以像这样使用打包的内容

```
 <ValidatedForm OnSubmit="SubmitForm" Model="validatedInstance">
        <input type="text" bind="@validatedInstance.ValidatedField" /><br />
        <ValidationErrorLabel Model="validatedInstance" FieldName="ValidatedField" /><br />
        <button type="submit">Validate</button>
    </ValidatedForm> 
```

## 结论

我不得不做了很多尝试来制作这个作品，问题更多的是与 Azure DevOps 而不是 Blazor 有关，但仍然。在这里，我们可以再次看到，Blazor 客户端几乎是一种生产就绪的技术，因为它与现有的工具和工作流兼容。

## 引用

*   [https://docs . Microsoft . com/en-us/dot net/core/tools/csproj # nu get-metadata-properties](https://docs.microsoft.com/en-us/dotnet/core/tools/csproj#nuget-metadata-properties)
*   [https://docs . Microsoft . com/en-us/nu get/create-packages/creating-a-package](https://docs.microsoft.com/en-us/nuget/create-packages/creating-a-package)
*   [https://github.com/aspnet/Blazor.docs/blob/master/docs/JavaScript-interop . MD](https://github.com/aspnet/Blazor.Docs/blob/master/docs/javascript-interop.md)