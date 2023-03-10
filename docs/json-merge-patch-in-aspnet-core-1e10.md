# ASP.NET 核心中的 JSON 合并补丁

> 原文：<https://dev.to/morcatko/json-merge-patch-in-aspnet-core-1e10>

REST 是众所周知的创建 web APIs 的常用模式。有许多 HTTP 动词，但是最常用于构建 REST 服务的是:

*   后期创建新项目
*   获取-读取项目
*   放置-更新/替换项目
*   删除-删除项目

这些方法通常足以让客户端对一个资源进行所有操作。但是，如果客户只想改变资源的特定属性，该怎么办呢？对于演示场景，假设我们有以下对象，我们想将武器属性改为“刀”

```
{
    name: "James Bond"
    age: "45"
    weapon: "Gun"
} 
```

伪 C#中的客户端代码:

```
srcJson = GET {resourceUrl}
model = Json.Parse<UserModel>(srcJson)
model.weapon = "Knife"
dstJson = Json.Serialize(model)
PUT {resourceUrl} 
```

这是显而易见的解决方案——获取资源，对其进行修改，然后将其放回服务器。根据您的架构和使用的语言，这可能有一些缺点。想象一下当你的用户模型缺少一些属性时会发生什么。它们会丢失，不会在 PUT 调用中发送到服务器。在默认 ASP.NET 核心/C#设置的情况下，它们将被解释为对默认值的更改，从而导致数据丢失。

# JSON 补丁

PUT 调用替换整个对象，而不是只执行部分资源更新。创建 JSON 补丁是为了通过发送操作列表而不是对象本身来完全按照您的需要修改资源。

JSON 补丁请求

```
[
 { op: "replace", path: "/weapon", value: "Knife" }
] 
```

JSON 补丁的实现是 ASP.NET 核心的一部分，
这种结构允许完全控制资源的所有属性，包括数组，但也倾向于相当繁琐，不太适合人类写/读。

# JSON 合并补丁

如果你正在寻找比 JSON 补丁更简单的东西，还有其他方法。
[JSON 合并补丁](https://tools.ietf.org/html/rfc7396)允许你以原模型的形式发送变更。没有操作列表，而是一个补丁对象，只包含与服务器上的原始资源合并的更改

JSON 合并补丁

```
{
    weapon: "Knife"
} 
```

结果会是

```
{
    name: "James Bond"
    age: "45"
    weapon: "Knife"
} 
```

与完整的 JSON 补丁相比，JSON 合并补丁是有限的。您不能删除属性(因为缺少属性意味着“没有更改”而不是“删除”)，并且数组操作也受到限制。

# 莫卡特科。JsonMergePatch

在 C#中使用 JSON 合并补丁并不容易。C#是静态类型语言，JSON 合并补丁格式期望完全相反——它通过属性(非)存在来定义操作。
[莫卡特科。AspNetCore.JsonMergePatch](https://www.nuget.org/packages/Morcatko.AspNetCore.JsonMergePatch) 是我为 ASP.NET 核心实现的 JSON 合并补丁。它的设计非常适合 ASP.NET 核心建筑。其 API 与完整 JSON 补丁的 API 相同。要使用它，您只需在启动类中初始化它，并创建一个接受 patch 对象的端点。

```
// Startup.ConfigureServices
services
    .AddMvc()
    .AddJsonMergePatch();

// Controller
[HttpPatch]
[Consumes(JsonMergePatchDocument.ContentType)]
public void Patch(JsonMergePatchDocument<UserModel> patch)
{
    ...
    patch.ApplyTo(backendModel);
    ...
} 
```

就是这样。
注意`Consumes`属性。您可以将它与完整的 JSON 补丁一起使用。Requests ContentType 是区分两个版本的一种方式。

如果您使用 swagger 为您的服务生成 API 文档，您将需要在您的 swagger 配置中添加一个`DocumentFilter`——更多细节请参见 github

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png) [莫尔卡特科](https://github.com/Morcatko) / [莫尔卡特科。AspNetCore.JsonMergePatch](https://github.com/Morcatko/Morcatko.AspNetCore.JsonMergePatch)

### 对 ASP.NET 核心的 JsonMergePatch 支持

<article class="markdown-body entry-content" itemprop="text">

# ASP.NET 核心 2.x 的 JSON 合并补丁支持

### JSON 合并补丁

*   [RFC 7396](https://tools.ietf.org/html/rfc7396)
*   执行类似于 JSON 补丁的部分资源更新
*   支持 Swagger
*   网络标准 2.0

```
C# object
var backendModel = new Model()
{
    Name = "James Bond"
    Age = "45"
    Weapon = "Gun"
}
JSON Merge Patch
{
    "Weapon": "Knife"
}

resulting C# object:
{
    Name = "James Bond"
    Age = "45"
    Weapon = "Knife"
} 
```

### 如何

样本见 testApp2.0

1.  安装[莫卡特科。JsonMergePatch](https://www.nuget.org/packages/Morcatko.AspNetCore.JsonMergePatch) nuget
2.  添加到您的启动类

```
using Morcatko.AspNetCore.JsonMergePatch;

public void ConfigureServices(IServiceCollection services)
{
    ...
    services
        .AddMvc()
        .AddJsonMergePatch();
    ...
} 
```

3.  在您的控制器中使用

```
using Morcatko.AspNetCore.JsonMergePatch;

[HttpPatch]
[Consumes(JsonMergePatchDocument.ContentType)]
public void Patch([FromBody] JsonMergePatchDocument<Model> patch)
{
    ...
    patch.ApplyTo(backendModel);
    ...
} 
```

4.  Swagger 配置(可选)

将这个类复制并粘贴到你的应用中-[https://github.com/Morcatko/Morcatko.AspNetCore . JsonMergePatch/blob/master/test/test app 2.0/jsonmergepatchdocumentoperationfilter . cs](https://github.com/Morcatko/Morcatko.AspNetCore.JsonMergePatch/blob/master/test/testApp2.0/JsonMergePatchDocumentOperationFilter.cs)

```
services.AddSwaggerGen(c =>
    {
        c.OperationFilter<JsonMergePatchDocumentOperationFilter>();
    }); 
```

### 如何进行单元测试

更多示例见`Morcatko.AspNetCore.JsonMergePatch.Tests.Builder.Json.Simple`类

```
Morcatko.AspNetCore.JsonMergePatch.Tests.Builder.Json
public void UnitTest()
{
    var model = new Model();
```

…</article>

[View on GitHub](https://github.com/Morcatko/Morcatko.AspNetCore.JsonMergePatch)