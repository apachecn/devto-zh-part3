# 使用 HttpClient 拦截进行测试

> 原文：<https://dev.to/stuartblang/testing-with-httpclient-interception-304f>

在我的上一篇文章中，我展示了如何从一个 swagger 文件中自动生成一个用于`HttpClientFactory`的类型化客户端。现在我想对客户端的行为进行修改，并需要进行单元测试，在这篇文章中，我将看看 [HttpClient 拦截](https://github.com/justeat/httpclient-interception/)库，以及我们如何在测试中有效地使用它。

看看下面的代码，它利用了我们之前生成的客户机。

```
var pet = await petStoreClient.GetPetByIdAsync(1, ct);
var firstTag = pet.Tags.FirstOrDefault(); 
```

这看起来很好，对不对？但是如果`Tags`是`null`会怎么样呢？那件事会发生吗？

快速解决方法是使用`?.`来传播空安全，但是也许我们希望我们的反序列化永远不允许集合为空，而是默认为空集合。

为了弄清楚当前的行为并记录我们希望它如何工作，我们应该编写测试。

我将用 F#编写这些测试，因为它太棒了！它不仅是一种非常好的语言，我想在我的测试中使用包含 JSON 的字符串，这在 C#中是很痛苦的。

## HttpClient 拦截

这个库真的很棒，它有一个流畅的 API，可以生成一个加载了一个`HttpClientHandler`的`HttpClient`，这个`HttpClientHandler`将拦截请求并返回配置好的响应，所以它非常适合单元测试。

流畅的 API 非常直观，你可以在 [README](https://github.com/justeat/httpclient-interception/) 上看到一些很棒的例子。

```
module NSwag.PetStore.Client.Tests.Tests

open System
open Xunit
open NSwag.PetStore.Client
open JustEat.HttpClientInterception

[<Fact>]
let ``Given missing collection property, we deserialize to an empty collection. 🚀`` () =

    let builder =
        HttpRequestInterceptionBuilder()
            .Requests().ForAnyHost().ForPath("/pet/1234")
            .Responds().WithContent """{
  "id": 0,
  "name": "doggie",
  "photoUrls": [
    "string"
  ],
  "status": "available"
}"""

    let options =  HttpClientInterceptorOptions().ThrowsOnMissingRegistration()
    builder.RegisterWith options |> ignore
    use innerClient = options.CreateHttpClient()

    innerClient.BaseAddress <- "http://test-host" |> Uri

    let client = PetStoreClient(innerClient)
    let pet = client.GetPetByIdAsync(1234L) |> Async.AwaitTask |> Async.RunSynchronously

    Assert.NotNull pet.Tags
    Assert.Empty pet.Tags 
```

有些事情要大声说出来:

*   我们用的是`xUnit`，另一个很好的选择是 [Expecto](https://github.com/haf/expecto) ，但是目前还没有骑手的测试跑步者，xUnit 在这里会工作得很好。xUnit 与 F#配合得很好，我们不需要类定义，注意我们在这里有一个模块，但是一旦我们想要使用`ITestOutputHelper`我们就必须切换到使用类。
*   F#中的函数名可以包含几乎任何东西，包括空格、标点符号、甚至表情符号(这意味着你必须使用它们！).
*   当我们使用三重引号字符串时，字符串文字可以包含`"`——这对 JSON 和 XML 片段来说很好。

就我们用 HttpClient 拦截器所做的事情而言，它几乎不需要解释，因为它完全是声明性的，我要添加的唯一注释是，在测试期间，您可能需要`ThrowsOnMissingRegistration`,因为这确保了一切都完全在内存中，没有任何东西通过并具体化为实际的 HTTP 请求。

<figure>[![](img/f6417bd309569ea4ea490a8c923be69d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CAYvCx_6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stu.dev/conteimg/2019/03/image.png) 

<figcaption>正如我们所怀疑的！</figcaption>

</figure>

对，所以如果 JSON 的`Tag`部分被省略，那么我们最终得到一个空集合，这不是我们想要的，所以让我们改变它。

NSwag 让我们以几种方式控制序列化设置，一种方式是通过`jsonSerializerSettingsTransformationMethod`设置，它让我们指向一个静态方法来配置`JsonSerializerSettings`。

经过大量的调查，我发现这是实现我想要的反序列化的最简单的方法:

```
using System;
using System.Collections.Generic;
using System.Reflection;
using Newtonsoft.Json;
using Newtonsoft.Json.Serialization;

namespace NSwag.PetStore.Client
{
    internal static class PetStoreSerializerSettings
    {
        public static JsonSerializerSettings TransformSettings(JsonSerializerSettings settings)
        {
            settings.DefaultValueHandling = DefaultValueHandling.Populate;
            settings.ContractResolver = new NullToEmptyListResolver();
            return settings;
        }
    }

    internal sealed class NullToEmptyListResolver : DefaultContractResolver
    {
        protected override JsonProperty CreateProperty(MemberInfo member, MemberSerialization memberSerialization)
        {
            var property = base.CreateProperty(member, memberSerialization);

            var propType = property.PropertyType;
            if (propType.IsGenericType && 
                propType.GetGenericTypeDefinition() == typeof(ICollection<>))
                property.NullValueHandling = NullValueHandling.Include;
            return property;
        }

        protected override IValueProvider CreateMemberValueProvider(MemberInfo member)
        {
            var provider = base.CreateMemberValueProvider(member);

            if (member.MemberType == MemberTypes.Property)
            {
                var propType = ((PropertyInfo)member).PropertyType;
                if (propType.IsGenericType && 
                    propType.GetGenericTypeDefinition() == typeof(ICollection<>))
                {
                    return new EmptyListValueProvider(provider, propType);
                }
            }

            return provider;
        }

        class EmptyListValueProvider : IValueProvider
        {
            readonly IValueProvider innerProvider;
            readonly object defaultValue;

            public EmptyListValueProvider(IValueProvider innerProvider, Type listType)
            {
                this.innerProvider = innerProvider;
                defaultValue = Array.CreateInstance(listType.GetGenericArguments()[0], 0);
            }

            public void SetValue(object target, object value) => innerProvider.SetValue(target, value ?? defaultValue);
            public object GetValue(object target) => innerProvider.GetValue(target) ?? defaultValue;
        }
    }
} 
```

如果有更简单的方法(而且我怀疑肯定有！)一定要在评论里让我知道。

当我们再次进行测试时...

<figure>[![](img/073348d5b5c4afc5113adef81f3301be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dgeRKpFs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stu.dev/conteimg/2019/03/image-1.png) 

<figcaption>那更好🙂</figcaption>

</figure>

## 其他选项同 HttpClient 拦截

在这种情况下，我希望我的测试包含一个 JSON 字符串文字，但是在大多数情况下，我们可以使用`WithJsonContent`方法，它接受一个对象并为我们序列化它(可选地使用序列化设置)。具有讽刺意味的是，如果我想这样做，在 C#中会更容易，因为匿名对象在 F#中还没有登陆(但是它是如此的接近！).

一个令人信服的替代方案是使用一个 [HTTP bundle](https://github.com/justeat/httpclient-interception#http-bundle-files) 文件，引用文档的话来说，“可以用来存储 HTTP 请求，以拦截相应的响应并将其存储为 JSON”。

## 关闭

HttpClient 拦截是[马丁·科斯特洛](https://twitter.com/martin_costello)的[创意，他总是在互联网上做惊人的事情。NET 核心社区。](https://github.com/justeat/httpclient-interception/graphs/contributors)

你可以在这个帖子的中找到所有的[代码。](https://github.com/slang25/nswag-petstore-client/tree/httpclient-interception)