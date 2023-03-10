# [swag buckle 和 API 版本控制]在 swag UI 中，过时属性不会被呈现为不推荐使用

> 原文：<https://dev.to/joni2nja/swashbuckle-and-api-versioning-obsolete-attribute-is-not-rendered-as-deprecated-in-swagger-ui-10f4>

这篇文章最初出现在[媒体](https://medium.com/@joni2nja/swashbuckle-and-api-versioning-obsolete-attribute-is-not-rendered-as-deprecated-in-swagger-ui-c44415111085)上

* * *

### 已将 ASP.NET 核心 2.1 升级到 2.2: [Swashbuckle 和 API 版本]过时属性不会在 Swagger UI 中呈现为已弃用

#### 经历废弃动作在 Swagger UI 中不被渲染为弃用？

您已经将 ASP.NET 核心 2.1 版应用升级到 2.2 版，并且正在使用:

*   [Swashbuckle。AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 版本 3.x
*   [API 版本控制(微软。AspNetCore.Mvc.Versioning)](https://github.com/Microsoft/aspnet-api-versioning) 版本 3.x

并且，你有一个或多个你的 API 控制器 ***动作*** 装饰成`Obsolete`？

很有可能，Swagger UI 会将您的`Obsolete`动作(端点)呈现为普通的 API 端点，而不是像您预期的那样“被否决”。

<figure>[![](img/605fa093e84de10effaf3226bed05f88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JNjx8Ik_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/728/1%2Avdw7IPfcOtyBR60vrznVzw.png) 

<figcaption>【删除】动作方法标记为作废</figcaption>

</figure>

<figure>[![](img/9488306c1694a1eb1530ff308f0c9be4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nL3XY3ve--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/947/1%2An-aRhnMVz4Xn1O6MJxZGXA.png) 

<figcaption>删除/api/v3/People/{id}应渲染为“已弃用”…</figcaption>

</figure>

那么，怎么了？我只是跟踪了来自 [API 版本](https://github.com/Microsoft/aspnet-api-versioning)示例的变更历史:

[微软/aspnet-API-版本控制](https://github.com/Microsoft/aspnet-api-versioning/blob/1a1a6b6629ee0801d3334fbceeb8412aa3b7ce1f/samples/aspnetcore/SwaggerSample/SwaggerDefaultValues.cs)

我们甚至可以用一个很酷的动画来查看 git 的更改历史，如下所示:

[![](img/8b2894d0ebd81bdeb46c3e162eaee687.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IHmqzZda--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A04OPJR_wGT4fQo44FzRIJQ.gif)

用这个[链接](https://github.githistory.xyz/Microsoft/aspnet-api-versioning/blob/1a1a6b6629ee0801d3334fbceeb8412aa3b7ce1f/samples/aspnetcore/SwaggerSample/SwaggerDefaultValues.cs)自己看。

还在吗？好的，这里我们有这条线:

```
operation.Deprecated = apiDescription.IsDeprecated(); 
```

它应该做的工作是弃用端点。但是有一个缺陷。它是*覆盖先前由`SwaggerGenerator`分配给某处的值*。

<figure>[![](img/3a50d8b8ce958cb4cce6d132493d9ea6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rMNrxRyz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAHeDsXLX8TiuNbH2zpsqAw.png) 

<figcaption>弃用是对“apiDescription”的真实评价。IsDeprecated()"</figcaption>

</figure>

这里我们可以看到`operation.Deprecated`已经是`true!`了让我们看看在这之前发生了什么。

<figure>[![](img/0a1df09e55addaa4bfc90a00390bcec7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qHOaN7Po--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkOxBeGnbkCDHLLz4NsTfuw.png)

<figcaption>[https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/192 aa 2d 70 f 057 fc 2805 f 58d 51 b 6 e 859 f1 f 6b 07 ce/src/swash buckle。AspNetCore.SwaggerGen/Generator/SwaggerGenerator.cs](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/192aa2d70f057fc2805f58d51b6e859f1f6b07ce/src/Swashbuckle.AspNetCore.SwaggerGen/Generator/SwaggerGenerator.cs)</figcaption>

</figure>

执行`apiDescription.IsDeprecated()`后，结果会是`false`。答对了。如果它已经是`true`，我们应该阻止它执行。

代替`=`，我们可以通过使用`|=`用[使](https://en.wikipedia.org/wiki/Short-circuit_evaluation)短路，这样一旦它被标记为`true`——不赞成使用，我们甚至都懒得看它。

现在，Swagger UI 应该像预期的那样呈现了。

<figure>[![](img/c07cb07ce618ed14d6da3eb5d5665534.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jWFxGfsl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/939/1%2ATTH9IjJJsYKiY_v2hgncIw.png) 

<figcaption>删除/api/v3/People/{id}被渲染为“已弃用”</figcaption>

</figure>

问题解决了！

我花了几个小时深入研究这个问题，想知道问题的根源是什么，因为它在升级之前就工作了。 [API 版本](https://github.com/Microsoft/aspnet-api-versioning)提供的样例代码有错吗？不完全是。它仅仅是一个样品。请理解，样本不会涵盖所有可能的情况。所以不能怪。但是，嘿，我认为样品可以更好。我知道这可能是一种边缘情况，但我会尝试向回购所有者报告，并可能发送一个 Pull 请求来修复示例代码，希望这将节省未来用户数小时的调试时间！

****更新*** *:我在这里开了期:*[*https://github . com/Microsoft/aspnet-API-versioning/issues/470*](https://github.com/microsoft/aspnet-api-versioning/issues/470)*】**