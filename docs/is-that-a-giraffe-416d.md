# 那是长颈鹿吗？

> 原文：<https://dev.to/tunaxor/is-that-a-giraffe-416d>

这几个月我一直在远方，忙于工作，也在寻找一些东西与 *Dev.to* 社区分享。

和*玩的太多了。net* 最近为我的个人项目，并试图最终解决它，以便我可以最终把它添加到我的`I can do that sh*t`腰带。到目前为止，一些前端 js 和节点都在这一带。我想再延长一点。

我希望在以后的帖子中分享更多用 C#和 Js 编写 UWP 应用程序的经验，但现在我将看看[长颈鹿](https://github.com/giraffe-fsharp/Giraffe)。

(如果您只想查看代码，可以跳过前两节)

### 首先 F#？

是的，我之前已经尝试了几十次，试图弄清楚如何在 F#中做一些有意义的事情，但是到目前为止，我的尝试有点受挫，因为我根本不理解 FP。

事实上，当你查看 FP 资源时，你经常会看到这些`mystical-hocus-pocus-magical`应用程序/术语，你经常只看到这些一行程序和一大堆函数，这些函数似乎在其他函数中有深度，这也没有帮助。互联网世界的一些讨论对于你首先应该如何做 FP 是相当权威的。

我从面向对象中学习，所以虽然我找到了小块功能的用例，但我从未完全掌握将这些功能块组合在一起做一件大事的想法，事实上这并不坏，我只是不擅长掌握这些 FP 资源，因为我真的没有什么东西来应用这些东西。

## 长颈鹿是什么？

Giraffe 在某种程度上是一个为函数式程序员构建在 ASP.NET 核心之上的库/框架，所以是的，你有你的 kestrel 服务器，访问通用 ASP.NET 依赖注入，中间件等等。正如作者在他的[博客](https://dusted.codes/functional-aspnet-core)中所说

> 理想情况下，作为一名 F#开发人员，我想用一个功能相当的框架来替换面向对象的 MVC 框架，但同时保留 ASP.NET 核心的其他产品。

这是他开始工作时的想法，在长颈鹿周围社区的帮助下，他做得非常好。

话虽如此...

### 有事分享

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [【鱼苗】](https://github.com/AngelMunoz) / [旋转](https://github.com/AngelMunoz/Giraffarig)

### 带有 MongoDB 和 Giraffe 的 F# Web API 的小框架

<article class="markdown-body entry-content container-lg" itemprop="text">

# Giraffarig！

这是我用[做的一些个人项目的 F#框架。网芯](https://dotnet.microsoft.com/download)、 [MongoDB](https://www.mongodb.com/) 和[长颈鹿](https://github.com/giraffe-fsharp/Giraffe)。

### 克隆后要做的事情

*   不要忘记将你的`Giraffarig`引用重命名为你的项目名
*   在`Properties/launchSettings.json`和代码中改变你的环境变量/变量名
*   在`Db.fs`中更改您的数据库名称
*   运行`dotnet tool restore`
*   运行`dotnet paket restore`

#### 放弃

我不擅长 F#。我想这种语言终于让我喜欢上了这个示例 API，我想我应该能够开始深入挖掘 F#的更多用例，而不仅仅是简单的 Jwt 登录。

#### 发现不对劲了吗？

我确实打算学习更多的 F#语言，但是首先我对神秘的涂抹/部分应用/组合(差不多足够了)不太感兴趣，所以请对我温和一点。

也就是说，请随时提出问题并指出您可能发现的不良模式…

</article>

[View on GitHub](https://github.com/AngelMunoz/Giraffarig)

现在请记住，我不是 F#忍者，甚至更远。我通过了解其他技术，如 express(Js)、ASP.NET 核心(C#版本)、Flask 和其他技术，掌握了允许我做那个框架的概念，所以我的功能 Fu 不是顶级的，但是我真的喜欢强调一些允许我在 F#中做一些事情的概念。

长颈鹿路线的基本块是这些`HttpHandler`功能

```
let routeHandler:HttpHandler =
    fun (next : HttpFunc) (ctx : HttpContext) -> task {
        return! Successful.OK "Hello World!" next ctx
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在我已经在 express 中工作了一段时间，这个函数对我来说似乎很熟悉(除了 lang + syntax y'know)

```
function (req, res, next) {
  res.send('Hello World!')
} 
```

Enter fullscreen mode Exit fullscreen mode

哈...兴趣...读了一些 Giraffe 文档后，我有点明白了，这些功能就像 express 的中间件！

您可以只使用多个小函数来传递请求的管道(或者您可以随意调用它)

所以我决定引入 MongoDB。net 驱动程序和登录/注册以及用户获取路径。

```
// Program.fs
let webApp =
    choose [
        GET >=>
            choose [
                route "/" >=> text "Hello world"
                route "/api/users" >=> authorize >=> getUsers
            ]
        POST >=>
            choose [
                route "/api/auth/login" >=> login
                route "/api/auth/signup" >=> signup
            ]
        setStatusCode 404 >=> text "Not Found" ] 
```

Enter fullscreen mode Exit fullscreen mode

这是我的路由器，两组 **GET** 和 **POST** routes
你可以在存储库中更好地看到，但这里的交易是一种你可能会发现的模式

```
route "/" >=> text "Hello world" 
```

Enter fullscreen mode Exit fullscreen mode

那个符号是一个有趣的函数，你可以在这里了解更多关于它的信息，但是基本上

> compose 组合器将两个 HttpHandler 函数组合成一个。

在这个例子中，我们使用函数 route，它有一个参数，然后将结果应用到文本函数，它也有一个参数，这样我们可以创建一个简单的 route，它将为我们提供一个`Hello World`
你可以在 getUsers 端点
上更好地看到它

```
route "/api/users" >=> authorize >=> getUsers 
```

Enter fullscreen mode Exit fullscreen mode

这里我们采用一个路由，然后是一个 authorize 函数，它将挑战请求，如果成功，它将继续执行`getUsers`函数。

现在，在 getUsers 函数之前，让我分享一下使用来自 F#的 mongo 驱动程序一点也不难

```
module Db
open MongoDB.Driver
open System

[<CLIMutable>]
type User = 
    { Id: BsonObjectId;  
      Name: string; 
      LastName: string; 
      Role: string; 
      Email: string; 
      Password: string; }
[<CLIMutable>]
type EndpointResult<'T> = { Count: int64; List: List<'T>; }

let connectionString = Environment.GetEnvironmentVariable  "MONGO_URL"

[<Literal>]
let DatabaseName = "fs_database_name"
let client = new MongoClient(connectionString)
let db = client.GetDatabase(DatabaseName)
let UserCollection = db.GetCollection<User> "fs_users" 
```

Enter fullscreen mode Exit fullscreen mode

很直接，对吧？稍后我们将使用那个`UserCollection`来做我们的用户查询

```
let getUsers:HttpHandler =
    fun (next : HttpFunc) (ctx : HttpContext) -> task {
        // a small helper function that gets limit/offset from query strings
        let (limit, offset) = getPagination ctx
        // no special filters, just give me that count
        let count = UserCollection.CountDocuments(Builders.Filter.Empty)
        let users = 
            UserCollection.Find(Builders.Filter.Empty)
                .Skip(Nullable(offset))
                .Limit(Nullable(limit))
                .ToEnumerable()
            // we have now our users let's get rid of sensitive information
            |> Seq.map(fun user -> userToDTO(user))
            |> Seq.toList
        // return the users plus the next and context so it can be composed
        return! Successful.OK ({ Count = count; List = users }) next ctx
    } 
```

Enter fullscreen mode Exit fullscreen mode

Aaaand...就这样，嘣你就有了一个可分页的端点！有点类似于你在 express 中做的事情。

回购协议中还有`Jwt authentication`，所以你可能也想看看，jwt 认证是用`asp.net core`完成的，所以这是 Giraffe 的一个好处，最终它只是 ASP.NET 核心！

### 结论

随着这个示例的启动和运行，我可以看到自己更频繁地使用 F#了(终于！)我很高兴我以前使用过 express，否则这可能是我在 F#上的又一次失败尝试:)
我还想提一下，我也尝试过在 F#上单独使用 alone 内核，但它太乱了，对我来说效果不太好。当我把它与 C#进行比较时，我有点觉得自己不合适

感谢你阅读我的无厘头！如果我在某些地方走得太远，我很抱歉。

一如既往，欢迎在下面分享反馈/评论！