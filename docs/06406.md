# 最小的无服务器功能

> 原文：<https://dev.to/azure/the-most-minimal-serverless-function-3cm1>

我一直在享受用 Azure 函数创建一些 API 的乐趣。Functions as a Service (FaaS)是“无服务器”产品的一个类别，它允许我编写代码并轻松地部署它，而不必担心代码本身是否工作以外的任何事情——供应服务器、安装依赖项和为闲置服务器付费都已成为过去。

不幸的是，第一次开始使用 Azure Functions 并不像我希望的那样顺利，因为我没有完全掌握构成 Azure Functions 开发流程的所有部分。

在这篇文章中，我们将通过手工创建最简单的函数来更好地理解 Azure Functions 是如何工作的。然后，我们将讨论为我们创建样板文件的各种可用工具，这样我们就不必这么做了。最后，当您使用这些工具来帮助创建 Azure 函数时，您应该对实际发生的事情有一个很好的了解。

### 创建项目

我们要做的第一件事是在我们的本地开发机器上创建一个 Azure Function 项目，它包含我们正在进行的任何项目的所有函数。这将包含我们可以签入源代码控制的函数的所有代码，以及我们可以用于函数的本地测试的一些配置。

一个项目至少有一个`host.json`文件，它指定了我们想要使用的运行时版本。这个看起来是这样的:

```
{  "version":  "2.0"  } 
```

有了这个文件，我们就有了一个 Azure Functions 项目！当然我们没有功能，所以它也没做太多。让我们创建一个函数。

### 创建功能

我们的函数将是一个节点函数。在 node 的情况下，最起码我们需要为我们的函数创建一个我们想要的任何名称的目录(例如，“MyHttpFunction”)，该目录有一个名为`index.js`的 JavaScript 文件，该文件有一个我们将在下面看到的导出函数和一个描述该函数的`function.json`文件。

为了通知函数运行时我们正在等待一个传入的请求，并且我们想要提供一个传出的响应，我们需要创建一个描述这些绑定的`function.json`(`MyHttpFunction/function.json`):

```
{  "bindings":  [  {  "type":  "httpTrigger",  "direction":  "in",  "authLevel":  "anonymous",  "methods":  [  "get"  ],  "name":  "req"  },  {  "type":  "http",  "direction":  "out",  "name":  "res"  }  ]  } 
```

在我们的`bindings`数组中的第一个对象说我们希望这个函数由一个 http 请求触发(由设置为“httpTrigger”的“type”指定)，它将需要匿名授权(也就是没有授权)，并且它将只在 http GET 请求上被触发。我们还必须提供一个“名称”,尽管我们可以随意称呼它，因为我们将它作为函数的参数来访问。通过将“方向”指定为“in ”,我们说这是一个触发我们函数的传入 http 请求。

第二个对象说我们想要创建一个“http”类型的绑定，我们将在我们的函数中设置一个名为“res”的键。通过将“方向”指定为“输出”，我们说我们的函数将返回或输出一个 http 响应。

接下来，让我们创建 JavaScript 文件`MyHttpFunction/index.js` :

```
module.exports = async function (context, req) { // req is the request that triggered this function
  context.res = { body: "Hello" } // we specified res as our response object in our bindings
} 
```

这个函数将简单地忽略传入的请求，并将响应的主体设置为“Hello”。

虽然我们在这种情况下使用 node，但是函数也适用于许多其他语言。对于其他一些语言，比如 C#，只需要代码文件就足够了，因为这些语言使用代码中的属性来指定信息——不需要目录或`function.json`文件。如果我们要创建一个最小的 C#函数，它可能看起来像这样:

```
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyFunctions
{
    public static class HttpTrigger
    {
        [FunctionName("MyHttpFunction")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            return (ActionResult)new OkObjectResult("Hello");
        }
    }
} 
```

我们可以通过安装 [Azure Functions CLI 工具](https://github.com/Azure/azure-functions-core-tools?WT.mc_id=devto-blog-rylevick)(我们可以在装有 brew `brew tap azure/functions; brew install azure-functions-core-tools`的 macOs 和装有 npm `npm i -g azure-functions-core-tools --unsafe-perm true`的 Windows 上轻松安装该工具)并运行以下命令来本地测试我们的功能:

```
func host start 
```

## 功能应用程序

现在我们有了一个项目和其中的一个函数。我们可以在本地运行这个功能，但显然我们希望将它部署到云中。

Azure 中的所有功能都包含在功能应用中。功能应用程序是一种将不同功能组合成逻辑组的方式。例如，如果您正在创建一个 API，您可能会在每个端点上有一个函数，并将所有这些函数组合到一个函数应用程序中。

功能应用需要你对一些事情做出决定:

*   你的职能部门会在哪里？这里最明显的选择是选择一个离你的用户最近的地方。
*   该功能运行在哪个操作系统上？因为您为函数编写的代码通常是跨平台的，您不必自己管理基础设施，所以您选择哪个平台并不重要。您可以从门户编辑 Windows 函数，但不能编辑 Linux 函数(我们将在后面讨论)，但这并不是一个巨大的优势。
*   我们应该使用哪种托管计划？:消费计划意味着你将只为你使用的东西付费。如果你有一个很大的需求高峰，你的应用程序可能会暂时变慢，因为 Azure 会自动为你的功能提供更多的容量。
*   我们将在哪个运行时堆栈上运行？:这需要匹配你开发函数所针对的任何语言运行时。在我们的例子中，我们需要选择节点。

### 创建功能 App

有几种方法可以创建一个 Azure Function 应用程序，但现在最简单的方法可能是使用 Azure 门户网站。

我们可以通过点击门户右上方的“创建资源”按钮，搜索“Azure Function”并按照向导进行操作来创建一个 Azure Function App。

### 发布我们的功能

一旦我们的功能应用程序被创建，它就等着我们向它发布功能。我们可以使用 [Azure CLI 工具](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest&WT.mc_id=devto-blog-rylevick)和我们用来在本地测试功能的 Azure Functions CLI 工具来做到这一点。你可以通过运行
来实现

```
az login # logs you into Azure
func azure functionapp publish $NAME_OF_THE_FUNCTION_APP_YOU_CREATED 
```

认识到这个工具用你的本地函数项目的内容替换 Azure Functions 应用的内容是很重要的。如果您已经将功能部署到该功能应用程序，这些功能将被覆盖。但是当我们第一次运行它时，它会简单地用我们本地项目的内容替换 Functions 应用程序的空内容。

一旦这样做了，你就有了一个正在运行的 Azure 函数！

## 减少锅炉板

虽然我们可以轻松地手动创建非常简单的函数，但有一些工具可以帮助我们完成所有这些步骤，这样我们就不必自己编写所有的样板文件。

### 创建 Azure 功能项目

使用 Azure Functions CLI 工具，我们可以运行`func init`来初始化一个项目。这将带您了解一些关于哪个语言运行时(例如 node、dotnet 等)的问题。)您想要使用的文件，最终它将为一个项目创建必要的文件，比如我们已经看到的`host.json`文件和其他一些帮助您定制函数环境的文件。

如果你喜欢 Visual Studio 代码，你可以使用 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions&WT.mc_id=devto-blog-rylevick)。安装好之后，你可以打开命令面板(cmd/ctrl+shift+p)并搜索“Azure Functions:Create New Project”来创建一个新项目。这也将带你通过向导体验创建一个类似于`func init`的功能。该命令还允许您创建第一个函数(但是如果您愿意，您可以选择`Skip for now...`)。

### 创建 Azure 函数

使用 Azure Functions CLI 工具，您可以运行`func new`来引导您完成创建函数的向导过程。你只需要考虑你想要什么样的函数触发器。

对于有 Azure Functions 扩展的 Visual Studio 代码用户，可以使用命令调板(cmd/ctrl+shift+p)，搜索“Azure Functions: Create Function”。这将带您体验一次与`func` CLI 工具类似的向导体验。

此外，你可以从 Azure 门户的功能应用程序屏幕创建一个功能(功能列表旁边有一个大加号按钮)。这样做的一个问题是，您没有可以保存在版本控制中的函数的任何本地副本。您最终会希望将您的函数所属的函数应用程序下载到您的本地计算机，以便您可以将其添加到您的版本控制系统中。这通常需要将本地存在的功能与刚刚在 Azure 上创建的功能合并，这可能是一个真正令人头疼的问题...

您还可以对正在运行的函数进行编辑，但是如果您希望在版本控制中反映这些更改，您需要在本地项目中重复这些更改。我的建议是尽量避免从门户创建或编辑功能。

### 在 Azure 中创建功能 App

如果你安装了 Azure CLI 工具，你可以运行`az functionapp create`以及所需的选项。专业提示:通过使用[云外壳](https://docs.microsoft.com/en-us/azure/cloud-shell/overview?WT.mc_id=devto-blog-rylevick)特性，你可以在门户中使用 Azure CLI 工具，该特性为你提供一个已经安装了该工具的 bash 或 powershell 命令行。

从 Visual Studio 代码中，使用命令调板(cmd/ctrl+shift+p)，可以搜索“Azure Functions:在 Azure 中创建函数 App”。这将带您体验类似于 Azure CLI 体验的向导体验。

## 结论

总的来说，记住函数不需要太多的设置就能运行是很重要的。你需要确保你已经为一个本地项目创建了一个功能应用程序和最小的部分。每个函数最多需要 2 个小文件来运行。

到目前为止，你至少应该已经了解了运行 Azure 功能需要什么，以及创建这些功能的许多方法。如果你想继续下去，我推荐你去看看函数上的 [MS Learn 学习路径！如果你想了解更多，请随时在](https://docs.microsoft.com/en-us/learn/paths/create-serverless-applications?WT.mc_id=devto-blog-rylevick)[推特](https://twitter.com/ryan_levick)上联系我！