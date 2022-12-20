# Azure Functions v2 触发器和与 PowerShell 核心的绑定

> 原文：<https://dev.to/omiossec/azure-functions-v2-trigger-and-binding-with-powershell-core-2f71>

*这篇帖子是关于 Azure Functions v2 中的 PowerShell。这是如何使用 powershell 的基本触发器和绑定。*

通过 PowerShell 核心、托管身份和 AZ 模块的集成，PowerShell Azure 功能可以用作基于事件的无服务器自动化工具。

为什么基于事件？Azure Functions 代码由事件触发。它可以是 http 调用、事件网格中的日志消息、队列中的消息、容器中的新 blob...

事件数据甚至不需要被函数知道(大部分时候可能有用)，事件发生的事实就足够运行代码了。

这是你可以和 Azure 函数一起使用的触发器列表

*   一滴
*   长队
*   超文本传送协议
*   Cosmo 数据库
*   事件网格
*   活动中心
*   MS 图形事件
*   服务总线
*   计时器

在一个函数中，代码只能由一个事件触发。只有一个触发器。如果相同的代码需要为不同的事件运行，比如队列中的新消息和容器中的新 blob，那么您需要两个函数。

除了触发器，还有绑定。绑定将资源与函数相关联。绑定允许您在函数中使用 Azure 资源，而无需在代码中管理到资源的连接。

绑定可用于输入和/或输出。这取决于资源。

| 粘合剂 | 方向 |
| --- | --- |
| 一滴 | 输入和输出 |
| 长队 | 输出 |
| 桌子 | 输入和输出 |
| CosmoDb | 输入和输出 |
| 活动中心 | 输出 |
| 超文本传送协议（Hyper Text Transport Protocol 的缩写） | 输出 |
| 图表 Excel | 输入和输出 |
| 图形 Onedrive | 输入和输出 |
| 图表电子邮件 | 输出 |
| 图形事件 | 输入和输出 |
| 通知中心 | 输出 |
| SendGrid | 输出 |
| 服务总线 | 输出 |
| 信号员 | 输入和输出 |

要从 run.ps1 中的代码访问输入对象，必须在 run.ps1 的 param 部分添加 function.json 文件中的绑定名称，要将数据推送到绑定，必须在 Push-OutputBinding cmdlet 的 name 参数中使用绑定名称。

看一看 function.json

```
{  "bindings":  [  {  "name":  "TriggerBlob",  "type":  "blobTrigger",  "direction":  "in",  "path":  "workitems/{name}",  "connection":  "AzureWebJobsStorage"  },  {  "type":  "blob",  "name":  "inputBlob",  "path":  "incontainer/{name}.json",  "connection":  "AzureWebJobsStorage",  "direction":  "in"  },  {  "type":  "blob",  "name":  "outputBlob",  "path":  "outcontainer/{rand-guid}.json",  "connection":  "AzureWebJobsStorage",  "direction":  "out"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

以及相关的 run.ps1

```
# Input bindings passed via param  param([byte[]]  $TriggerBlob,  [String[]]  $InputBlob,  $TriggerMetadata)  # Output passed via Push-OutputBinding  Push-OutputBinding  -name  OutputBlob  -value  $SomeValue 
```

Enter fullscreen mode Exit fullscreen mode

每个触发器和绑定系列都有自己的要求和语法。他们使用扩展(微软。之前需要安装的 Azure.WebJobs.Extensions.xxxx)。

让我们来看看一些触发器和绑定，http、队列、表和 blob

**HTTP**

这个触发器代表了我们在谈论基于 web 的 API server less 时的想法。该触发器响应任何 http 谓词上的任何 web 调用。

它使用微软。Azure.WebJobs.Extensions.Http 包。此外，http 函数使用 system.net 命名空间中的对象，它应该被导入

```
Using namespace System.Net 
```

Enter fullscreen mode Exit fullscreen mode

在 function.json 中是这样的

```
{  "authLevel":  "function",  "name":  "req",  "route":  "",  "type":  "httpTrigger",  "direction":  "in",  "methods":  [  "get",  "post"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

其中:

***授权级别***
更改该功能的授权级别。Azure Functions 应用程序是一个 web 应用程序，它对互联网上的每个人开放。

有 3 种授权级别:

*   匿名:不需要认证

*   功能:需要一个键来访问该功能，该键是该功能的唯一键，功能应用程序中的其他功能将有不同的键。该密钥需要包含在查询中的代码 http 参数中，或者包含在具有字段 x-function-key 的 http 报头中。

*   Admin:与 function 的原理相同，但使用主密钥，该密钥对于 Function 应用程序中的所有功能都是相同的。

还有其他方法来保护功能。我们可以通过使用 WebApp Plateforme 功能中的访问限制来应用网络限制，也可以将功能应用程序计划从消费更改为对网络有更多的控制。
对于生产 API，还应该使用 Azure API 管理和/或 App 服务认证(要使用 Azure AD、Twitter、脸书、...账户)

***route***
它让你定义一个你期望看到请求的 url 模板。比如*products/{ product category:alpha }/{ id:int？}* 定义给参数 ProductCategory 和 ID(可选)。

***方法***
让你为函数限定动词。默认情况下，所有 http 动词都可以使用。使用这个 json 数组来限制动词

***方向***
默认情况下，在，这是一个触发器

***Name***
将在 run.ps1 中用作参数的绑定的名称

run.ps1 中的这个参数是一个 HttpRequestContext 对象。该对象的属性是:

*   body:post http 请求的主体
*   标题:Http 标题字典
*   方法:表示请求的 http 谓词的字符串
*   参数:获取参数字典
*   查询:查询中所有参数的字典
*   url:表示用于触发函数的 URL 的字符串

http 触发器不需要有 HTTP 输出。在这种情况下，该函数返回 204 no content http 代码。

为了返回响应，我们需要一个 http 输出绑定。

```
{  "type":  "http",  "direction":  "out",  "name":  "Response"  } 
```

Enter fullscreen mode Exit fullscreen mode

该字段名将在 Push-OutputBinding cmdlet 的 name 参数中使用。

value 中使用的对象必须是 HttpResponseContext 自定义类型对象。

它可以包含一个 statuscode(默认为[HttpStatusCode]::OK，但它可以是任何 StatusCode [见 MS Doc](https://docs.microsoft.com/en-us/uwp/api/windows.web.http.httpstatuscode) )。一个内容类型 content Type，默认为 text/plain，一个用于内容协商的 boolean，EnableContentNegotiation，一个用于设置自定义头的字典头和一个 body 对象，它可以是一个字符串或一个字节数组。

```
$HttpResponse  =  [HttpResponseContext]@{  StatusCode  =  $HttpStatus  Body  =  $RestultResponse  |  ConvertTo-Json  ContentType  =  "Application/json"  }  Push-OutputBinding  -Name  Response  -Value  $HttpResponse 
```

Enter fullscreen mode Exit fullscreen mode

**基于存储账户的触发和绑定**
存储账户与功能 App 关联。此存储帐户将功能应用程序文件存储在共享中。在创建存储绑定或触发器时，您需要将存储帐户的连接名称添加到 function.json 中的绑定中。

存储绑定和触发器与 Microsoft . azure . web jobs . extensions . storage 一起使用。

**队列**

在存储触发器和绑定中，队列是最简单的类型。

```
"bindings":  [  {  "name":  "QueueItem",  "type":  "queueTrigger",  "direction":  "in",  "queueName":  "mystoragequeue",  "connection":  "AzureWebJobsStorage"  },  {  "type":  "queue",  "name":  "outputQueueItem",  "queueName":  "vmalertequeue",  "connection":  "AzureWebJobsStorage",  "direction":  "out"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

该函数在名为 mystoragequeue 的队列中添加新消息时运行。消息通过名为 QueueItem 的参数发送给函数。QueueItem 是一个字符串。

```
param([string]  $QueueItem,  $TriggerMetadata) 
```

Enter fullscreen mode Exit fullscreen mode

对于绑定，只有一个方向，out！你可以用字符串来表示这个值。

```
Push-OutputBinding  -Name  outputQueueItem-Value  "Test" 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果因为这样或那样的原因，函数无法处理消息，函数应用程序将尝试 4 次运行代码。如果代码仍然无法处理，邮件将被标记为中毒。

**表**

Azure 存储表只有输入和输出绑定。在表中添加新元素时没有触发器(为此，您应该看看 Cosmo DB 触发器)。

在

```
{  "type":  "table",  "name":  "inputTable",  "tableName":  "inTable",  "take":  50,  "connection":  "AzureWebJobsStorage",  "direction":  "in",  "partitionKey":  "tablein",  "rowKey":  "1"  } 
```

Enter fullscreen mode Exit fullscreen mode

当该函数被另一个对象(Blob 存储、队列、Http 等)触发时，它将从 tablename 字段中查找表 nammed 中的数据。Take 将限制返回的行数，partitionkey 和 rowkey 将查询限制为这两个元素。

TableName 是绑定中唯一的必填字段。

在这个例子中，参数 inputTable 返回一个 hash 数组，每行一个 hashtable。

出局

```
{  "type":  "table",  "name":  "outputTable",  "tableName":  "outTable",  "connection":  "AzureWebJobsStorage",  "direction":  "out"  } 
```

Enter fullscreen mode Exit fullscreen mode

要向表发送数据，只需发送一个带有 push-outputBinding 的对象。对象必须是哈希表或哈希数组。

在每个哈希表中，必须至少有两个字段，partitionKey(分区的标识符)和 rowkey，分区键在分区中必须是唯一的。

注意属性名区分大小写，并且限制在 256 个字符内

```
$entity1  =  @{  partitionKey  =  'testtable'  rowKey  =  (new-guid).guid  name  =  "Valeur 1"  }  $entity2  =  @{  partitionKey  =  'testtable'  rowKey  =  (new-guid).guid  name  =  "Valeur 2"  }  $arrayOut  =  @($entity1,$entity2)  Push-OutputBinding  -Name  outputTable  -Value  $arrayOut 
```

Enter fullscreen mode Exit fullscreen mode

**斑点**

Blod 容器可以作为触发器、输入和输出绑定。

每次从与函数连接关联的存储帐户向 Blob 容器中添加新对象时，blob 触发器都会运行该函数。对象内容作为参数发送给函数。

```
{  "name":  "InputBlob",  "type":  "blobTrigger",  "direction":  "in",  "path":  "triggerblob/{name}",  "connection":  "AzureWebJobsStorage"  } 
```

Enter fullscreen mode Exit fullscreen mode

名称在 run.ps1 中用作参数。默认情况下，它是 object 的数组，但也可以使用 string(例如 json 文件)、stream 或 textreader。

Path 表示查找对象的路径。我们可以在这里使用过滤器，{name}。json

运行库确保在容器中添加新对象时，该函数只运行一次。

run.ps1 中的参数包含对象的内容。要获得关于对象本身的信息，您需要使用 triggermetadata 参数。

该对象包含:

BlobTrigger，对象的路径
URI，对象的 URL
属性一个 BlobProperties 对象

使用 blob 作为输入类似于触发器期望来自博客的数据不是来自新的 blob 对象。

我们需要给出现有对象的完整路径。您还可以使用一个模式来查找对象或触发器的类型(例如，如果队列触发器中的消息包含一个对象的名称，您可以使用它作为文件路径中的名称*" path ":" samples-work items/{ queue trigger } "*)

输出 Blob

{
"name": "InputBlob "，
"type": "blobTrigger "，
"direction": "in "，
" path ":" output blob/{ rand-guid }。json "、
"连接":" azurewebjobstorage "
}

当运行时在容器中创建对象时，使用 Rand-guid 可以让您拥有随机名称。

使用 blob 触发器可能不是最好的方法。冷启动会使功能延迟几分钟。事件网格是使用 blob 的另一种解决方案。

还有很多其他可用的触发器和绑定，EventGrid，CosmoDb，…但是 PowerShell 仍然处于预览阶段，你可能会发现一些错误和限制。

同样，功能的默认超时是 2 分 30 秒。即使你能改变这个座位，这也不是一个好主意。Azure 函数是一个无服务器的工具，每个函数需要执行一个特定的任务并且只有一个任务，队列消息(存储队列和服务总线)可以用来协调函数之间的动作。

Azure Function 包括免费授权、100 万次执行和 40 万 Gb/s(运行函数所需的秒数*运行它所需的内存)。