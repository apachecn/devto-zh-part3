# 具有 PowerShell Azure 功能和 Azure 容器实例的事件驱动的无服务器容器

> 原文：<https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b>

今天，Azure Functions 团队[宣布了他们的 V2 运行时对 PowerShell 支持的公开预览](https://azure.microsoft.com/blog/serverless-automation-using-powershell-preview-in-azure-functions/?WT.mc_id=devto-blog-antchu)(PowerShell 之前在 V1 是一种实验语言)。

在 Azure Functions V2 中，PowerShell 不仅仅是另一种编写无服务器应用的语言；它为许多事件驱动的自动化场景打开了大门，以前使用 Azure 函数很难实现这些场景。它使用了由 PowerShell 团队构建的全新的 [PowerShell 核心语言工作器](https://devblogs.microsoft.com/powershell/public-preview-of-powershell-in-azure-functions-2-x/?WT.mc_id=devto-blog-antchu)。

使用[托管身份](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview?WT.mc_id=devto-blog-antchu)，PowerShell Azure Function app 可以自动使用系统管理的 Azure Active Directory 身份进行身份验证。当与 [Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?WT.mc_id=devto-blog-antchu)结合时，我们可以编写 Azure 函数，可以安全地管理 Azure 中的任何资源。

在本文中，我们将看看如何使用 PowerShell Azure 函数和 [Azure 容器实例](https://docs.microsoft.com/azure/container-instances/?WT.mc_id=devto-blog-antchu)来触发和监控批处理作业。

## 概述(TL；博士)

### 我们在做什么？

有两种类型的工作负载传统上很难在 Azure Functions 这样的无服务器功能平台上运行:

*   应用程序，如复杂的批处理作业，难以重构为无服务器功能，但可以打包到一个容器中
*   需要运行超过平台允许的最大持续时间的无服务器功能(然而，在应用服务和高级计划中运行的 Azure 功能没有持续时间限制)

有了 PowerShell Azure Functions，我们可以对事件(如 HTTP 请求或队列消息)做出反应，并使用 Azure Container Instances 启动我们的容器化工作。Azure 函数也可以用来监控作业的进度。

### 我们是怎么做到的？

[![architecture](img/486983061477940ce286926d3e0ead69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vmrqa-SB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u1u2qxltfmc2jyzzg4db.png)

我们将构建一个 Azure 函数应用程序来启动和监控容器化的作业。

1.  通过向`/api/jobs`发送 HTTP POST 来触发作业；Azure Functions 将作业提交到 Azure 存储队列，并返回一个 HTTP 响应，其中包含状态代码`202 Accepted`和包含 URL `/api/jobs/{JobId}`的`location`头。
2.  一个函数将被队列消息触发，并运行`New-AzContainerGroup` cmdlet 来启动我们的作业。
3.  向`/api/jobs/{JobId}`发出 HTTP GET 请求，以监控作业的进度。

## 关键棋子

### Azure 容器实例

Azure 容器实例是在 Azure 中运行容器的最简单的方法。我们可以使用它来运行容器，而不必担心基础设施的供应，我们只需为使用的资源付费。

我们将在 Azure Function 应用程序中使用 Azure PowerShell 模块来创建和监控 ACI 中的容器。

### Azure 函数

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/?WT.mc_id=devto-blog-antchu) 是 Azure 的旗舰无服务器平台。无服务器功能可以由几十个事件触发，我们可以用 C#、JavaScript/TypeScript、Java、Python 编写，从今天开始:PowerShell。(Python 和 PowerShell 目前都在公开预览中。)

PowerShell Azure 函数使用[PowerShell Core](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?WT.mc_id=devto-blog-antchu)——一个在 Windows、Linux 和 macOS 上运行的较新版本的 PowerShell。我们可以访问 PowerShell Core 支持的所有模块，包括新的 Azure PowerShell 模块，它可以轻松管理 Azure 中的资源。

### 托管身份

[托管身份](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview?WT.mc_id=devto-blog-antchu)(以前称为托管服务身份或 MSI)自动为我们的 Azure 资源提供 Azure 活动目录服务主体，包括 Azure 功能。

### Azure 存储队列

每个 Azure 功能应用都包含一个 Azure 存储帐户。Azure 存储支持[队列](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction?WT.mc_id=devto-blog-antchu)；我们将使用它们异步触发 Azure 函数来启动 ACI 运行作业。

## 建立功能 app

### 入门

#### 源代码

我们正在构建的应用程序的源代码在 [GitHub](https://github.com/anthonychu/functions-powershell-run-aci) 上。

#### 工具

Visual Studio Code 几乎是我最喜欢的编辑器。它对 Azure 功能以及 PowerShell 都有很大的支持(甚至在 macOS 上！).首先，安装 [Azure Functions 核心工具](https://docs.microsoft.com/azure/azure-functions/functions-run-local?WT.mc_id=devto-blog-antchu)、 [Azure Functions VS 代码扩展](https://code.visualstudio.com/tutorials/functions-extension/getting-started?WT.mc_id=devto-blog-antchu)和 [PowerShell 核心](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-macos?view=powershell-6&WT.mc_id=devto-blog-antchu)。

要使用函数核心工具`func`创建函数应用程序(版本 2.6.1048 及以上，函数运行时版本 2.0.12408 及以上)，运行此命令并选择“PowerShell”:

```
func init 
```

#### 本地配置

对于本地开发，Azure Functions 支持将环境变量放在一个`local.settings.json`文件中。

这个项目需要几样东西。一个是 Azure 中的存储帐户及其名为`AzureWebJobsStorage`的本地设置中的连接字符串。

另一个是我们已经创建的 Azure 中资源组的名称(应用设置`ResourceGroupName`)。这将是创建容器实例的地方。

#### Azure PowerShell 模块

在我们开始编码之前，我们需要确保我们的应用程序和本地环境配置为运行 Azure PowerShell 模块。

在`host.json`中，确保`managedDependency`被启用。

```
{
  "version": "2.0",
  "managedDependency": {
    "enabled": true
  }
} 
```

并确保 function 应用程序的根目录下有一个名为`requirements.psd1`的文件，它告诉 Azure Functions 我们需要`Az`模块可用:

```
@{  'Az'  =  '1.*'  } 
```

最后，我们需要通过运行这个命令来确保我们登录到 Azure。(这仅在本地需要。在 Azure 中运行时，应用程序使用应用程序的托管身份登录。我们以后再谈这个。)

```
Connect-AzAccount 
```

我们的功能应用将由三个功能组成:

*   **RequestJob** - HTTP 触发的函数，用于将作业排队并返回状态 URL
*   **StartJob** -创建 Azure 容器实例来运行实际作业的队列触发函数
*   GetJobStatus -返回作业状态的 HTTP 触发函数

### RequestJob 函数

`RequestJob`函数在`/api/jobs`响应 HTTP POST。它生成一个作业 ID(GUID ),并将其作为消息添加到 Azure 存储队列中。然后，它返回一个 HTTP 状态`202 Accepted`和一个包含用于检查作业状态的 URL 的`location`头。

PowerShell 函数由一个以函数名命名的文件夹组成，其中包含两个文件:`function.json`和`run.ps1`。

描述函数的触发器和绑定。绑定用于从其他服务读取附加信息或向其他服务写入信息。我们将使用存储队列绑定将消息输出到队列中。

```
// RequestJob/function.json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "post"
      ],
      "route": "jobs"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "JobQueue",
      "queueName": "%JobQueueName%"
    }
  ]
} 
```

该函数由对名为“jobs”的路由的 HTTP POST 请求触发(这将生成一个`/api/jobs`的 HTTP 路由)。此外，该函数配置了函数级授权，这意味着当它在 Azure 中运行时，必须提供正确的授权代码才能被触发。我们用它来为我们的函数提供一点安全性，这样作业就不会被匿名请求启动。

我们使用绑定表达式`%JobQueueName%`代替队列名的硬编码值。我们可以在我们的功能 app 中配置这个值作为 app 设置。

```
# RequestJob/run.ps1  using  namespace  System.Net  param($Request,  $TriggerMetadata)  $ContainerName  =  (New-Guid).ToString()  $StatusUrl  =  "/api/jobs/$($ContainerName)"  Push-OutputBinding  -Name  JobQueue  -Value  $ContainerName  Push-OutputBinding  -Name  Response  -Value  ([HttpResponseContext]@{  StatusCode  =  [HttpStatusCode]::Accepted  Headers  =  @{  "Location"  =  $StatusUrl  "Content-type"  =  "application/json"  }  Body  =  $StatusUrl  }) 
```

该函数生成一个 GUID，用于命名 Azure 容器实例。它也是我们将用来构建状态 URL 的作业 ID。

使用我们在`function.json`中定义的名为`JobQueue`的队列绑定，该函数将作业 ID 输出到我们的队列中。

它还使用 HTTP 输出绑定返回一个带有包含状态 URL 的`location`头的`202 Accepted`状态代码。

我们的函数可以通过向`/api/jobs?code=<function-auth-code>`发出 HTTP POST 请求来调用。在本地运行时，功能授权代码可以留空。在 Azure 中运行时，我们可以使用 Azure 门户打开函数来定位函数的完整 URL，包括授权代码。一旦被调用，该函数将返回如下响应:

```
HTTP/1.1 202 Accepted
Content-Length: 46
Content-Type: application/json; charset=utf-8
Location: /api/jobs/2b2ed845-64e1-4b08-90f2-cf485ed4bdb4
Date: Mon, 22 Apr 2019 23:24:21 GMT

/api/jobs/2b2ed845-64e1-4b08-90f2-cf485ed4bdb4 
```

我们稍后将构建名为`GetJobStatus`的函数，当我们跟随在`location`头中指定的 URL 时将调用该函数。

### 启动作业功能

现在我们有了一个将作业 id 放在队列中的函数，我们需要一个由队列中的消息触发并启动作业的函数。

我们函数的`function.json`有一个队列触发器:

```
// StartJob/function.json
{
  "bindings": [
    {
      "name": "JobId",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%JobQueueName%"
    }
  ]
} 
```

函数体看起来像这样:

```
# StartJob/run.ps1  param([string]  $JobId,  $TriggerMetadata)  New-AzContainerGroup  -ResourceGroupName  $env:ResourceGroupName  -Name  $JobId  `  -Image  alpine  -OsType  Linux  `  -Command  "/bin/sh -c `"for i in ``seq 1 30``; do sleep 1; echo `$i; done`""  `  -RestartPolicy  Never 
```

它从队列消息中接收作业 ID，并使用`New-AzContainerGroup` cmdlet 启动运行我们作业的容器。出于演示的目的，我将简单地使用 Alpine Linux 容器并运行一个循环，在 30 秒内每秒输出一个数字。希望你的应用程序比这更有趣。

这就是使用 PowerShell Azure 函数在 ACI 中启动容器有多容易！

### GetJobStatus 函数

最后，我们需要一个 HTTP 端点来从 ACI 中检索容器的状态。这个函数可以是匿名的，因为它的路由包括很难猜测的作业 ID 的 GUID。

```
// GetJobStatus/function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get"
      ],
      "route": "jobs/{JobId}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
} 
```

```
# GetJobStatus/run.ps1  using  namespace  System.Net  param($Request,  $TriggerMetadata)  $JobId  =  $Request.Params.JobId  $JobStatus  =  Get-AzContainerGroup  -ResourceGroupName  $env:ResourceGroupName  -Name  $JobId  $JobLogs  =  Get-AzContainerInstanceLog  -ResourceGroupName  powershell-func-aci  -ContainerGroupName  $JobId  If  ($JobStatus.State  -eq  "Succeeded")  {  $StatusCode  =  [HttpStatusCode]::OK  }  ElseIf  ($JobStatus.State  -eq  "Failed")  {  $StatusCode  =  [HttpStatusCode]::InternalServerError  }  Else  {  $StatusCode  =  [HttpStatusCode]::Accepted  }  Push-OutputBinding  -Name  Response  -Value  ([HttpResponseContext]@{  StatusCode  =  $StatusCode  Headers  =  @{  "Content-type"  =  "application/json"  }  Body  =  @{  "state"  =  $JobStatus.State  "logs"  =  $JobLogs  }  }) 
```

该函数从 HTTP 路由参数中提取作业 ID，并调用`Get-AzContainerGroup`来检索 ACI 的状态。

此外，它调用`Get-AzContainerInstanceLog`来检索容器可能已经输出的任何日志。

我们返回状态和日志。如果容器仍在运行，我们返回一个 HTTP 状态代码`202 Accepted`。如果完成了，我们返回`200 OK`(成功)或`500 Internal Server Error`(失败)。

下面是一个调用状态 URL 的示例，它是在容器仍在运行(`/api/jobs/2b2ed845-64e1-4b08-90f2-cf485ed4bdb4`)时调用上面的示例`RequestJob`返回的:

```
HTTP/1.1 202 Accepted
Content-Length: 85
Content-Type: text/plain; charset=utf-8
Date: Mon, 22 Apr 2019 23:24:42 GMT

{
  "state": "Running",
  "logs": "1\n2\n3\n4\n5\n6\n7\n8\n9\n10\n11\n12\n13\n14\n"
} 
```

这是完成后的样子:

```
HTTP/1.1 200 OK
Content-Length: 154
Content-Type: text/plain; charset=utf-8
Date: Mon, 22 Apr 2019 23:25:30 GMT

{
  "state": "Succeeded",
  "logs": "1\n2\n3\n4\n5\n6\n7\n8\n9\n10\n11\n12\n13\n14\n15\n16\n17\n18\n19\n20\n21\n22\n23\n24\n25\n26\n27\n28\n29\n30\n"
} 
```

## 部署到 Azure

### 配置 app 设置

我们首先要做的是创建一个功能 app。创建应用后，在 Azure 功能应用设置中，配置以下设置:

*   `FUNCTIONS_WORKER_RUNTIME` -确保设置为`powershell`
*   `ResourceGroupName` -应用程序将在其中创建容器实例的资源组的名称
*   `JobQueueName` -保存待处理作业的存储队列的名称

### 配置托管身份

托管身份自动在 Azure AD 中为应用程序提供服务主体。我们的函数可以使用身份来验证 Azure PowerShell 模块并执行命令。

在 PowerShell Azure Functions 项目中，我们可以创建一个名为`profile.ps1`的脚本。在内部，我们检查应用程序是否运行在受管理的身份启用环境中，并使用该身份登录 Azure PowerShell。这在 PowerShell Azure Functions worker 的每次“冷启动”期间执行。

```
# profile.ps1  if  ($env:MSI_SECRET  -and  (Get-Module  -ListAvailable  Az.Accounts))  {  Connect-AzAccount  -Identity  } 
```

为了让我们的函数应用程序能够在我们在设置中指定的资源组中创建容器实例，我们必须授予应用程序的托管身份*贡献者*对资源组的访问权限。

[![Assign contributor role](img/823c2c1ee37f95ddebe328f37d40cc33.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZMHffyoF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sk1tmmzi5mk2pv666urn.png)

### 部署功能核心工具

配置好应用程序后，我们可以通过运行以下函数核心工具命令来部署我们的应用程序:

```
func azure functionapp publish <name of the function app> 
```

### 测试 app

一旦应用程序被部署，在 Azure 门户中打开它并选择`RequestJob`功能。点击*获取功能 URL* ，获取包括授权码在内的功能 URL。

[![Get function URL](img/1547dcf1b6a4ba168d4cacb23d57c14c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HiNH1M1R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mcl8k9n569hkq4srk5xt.png)

使用类似 [Postman](https://www.getpostman.com/) 这样的 app，向函数发出 POST 请求，开始一项工作。该函数将使用状态 URL 进行响应。向该 URL 发出 GET 请求，以检索作业的当前状态和日志。

[![Run with Postman](img/4ee8892286e3f1ae77249cb0baf4ac5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9vNvzPbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mndgo9g3lzj07v742uxi.png)

## 资源

*   [PowerShell for Azure Functions 发布会](https://azure.microsoft.com/blog/serverless-automation-using-powershell-preview-in-azure-functions/?WT.mc_id=devto-blog-antchu)
*   【PowerShell 的 Azure Functions 开发者指南
*   [PowerShell 功能快速入门](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-powershell?WT.mc_id=devto-blog-antchu)
*   [GitHub 上的源代码](https://github.com/anthonychu/functions-powershell-run-aci)