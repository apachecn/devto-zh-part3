# 带有 F#的 Azure 函数

> 原文：<https://dev.to/azure/azure-functions-with-f-2l3c>

我开始着手一个新项目，在这个项目中，我将使用 [Azure Functions v2](https://docs.microsoft.com/en-us/azure/azure-functions/?WT.mc_id=devto-blog-aapowell) 作为一个简单的 API 后端。

Azure 函数支持许多不同的语言，如 [Java](https://azure.microsoft.com/en-us/blog/announcing-the-general-availability-of-java-support-in-azure-functions/?WT.mc_id=devto-blog-aapowell) 、 [Python(在编写时预览)](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-python?WT.mc_id=devto-blog-aapowell)、 [TypeScript(当然还有 JavaScript)](https://azure.microsoft.com/en-us/blog/improving-the-typescript-support-in-azure-functions/?WT.mc_id=devto-blog-aapowell) ，当然还有 C#。那么有这么多选择，我想选择什么呢？

嗯，我自然决定用 F#，它的*有点像 v1 中的*。毕竟，它是一种 CLR 语言，所以没有理由不像 C#那样在 v2 中工作。

但不幸的是，没有可用的模板，所以开始似乎有点棘手。

## 创建 F#函数应用程序

要创建一个 F#函数应用程序，最简单的方法是按照 [Visual Studio 代码指令](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-vs-code?WT.mc_id=devto-blog-aapowell)安装扩展。

一旦 VS 代码准备就绪，我们将创建一个新的[新功能项目](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-vs-code?WT.mc_id=devto-blog-aapowell#create-an-azure-functions-project)选择 C#作为语言。

现在到了棘手的部分，将您的`csproj`文件重命名为`fsproj`，并添加一个对`FSharp.Core`的引用。

你就大功告成了！

好吧，其实也没那么难，对吧！因为它都在 CLR 上，而且是一个. NET 核心应用程序，`dotnet` cli 工具将会工作！您甚至可以从 F#函数的 VS 代码中获得调试支持。

现在你已经准备好用 F#创建一个函数了！

下面是一个基本的 HTTP 触发器:

```
module HttpTrigger

open Microsoft.Azure.WebJobs
open Microsoft.AspNetCore.Mvc
open Microsoft.Azure.WebJobs.Extensions.Http
open Microsoft.AspNetCore.Http
open System.IO
open System.Text

[<FunctionName("HttpTrigger")>]
let httpTriggerFunction ([<HttpTrigger(AuthorizationLevel.Function, "post", Route = null)>] req : HttpRequest) =
    async {
        use reader = new StreamReader(req.Body, Encoding.UTF8)
        let! body = reader.ReadToEndAsync() |> Async.AwaitTask
        return OkObjectResult body
    } |> Async.StartAsTask 
```

Enter fullscreen mode Exit fullscreen mode

请注意，如果你用`async`做任何事情，你需要将它转换成`Task<'T>`来返回，因为函数主机期望的是异步的 C#任务 API，而不是 F#的异步工作流😦。

## 注意事项

这里有一个小警告，因为 VS 代码扩展不理解 F#你不能用它来给你的项目添加新函数，你必须手动完成，然后你必须知道你需要的 NuGet 包是什么。我发现，如果我需要查找类型及其包，只需打开另一个 VS 代码窗口并创建一个 C#窗口就足够简单了。

您还会发现`.vscode/settings.json`文件包含`"azureFunctions.projectLanguage": "C#"`。如果你愿意，你可以把它改成`F#`，但是它会给你一个警告，因为扩展不理解它。我把它保留为 C#，因为它不会困扰我。

## 结论

虽然工具可能不存在，但用 F#创建 Azure 函数真的没什么大不了的。