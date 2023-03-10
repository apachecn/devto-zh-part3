# 给予 TodoMVC 它应得的 API:第 1 部分- Todo 创建

> 原文：<https://dev.to/azure/giving-todo-mvc-the-api-it-deserves-part-1-todo-creation-19h4>

Todo MVC 是一个比较不同 JavaScript 框架的好方法，可以让你感受到如何构建一个比自述文件更复杂的应用程序。

在这个系列中，我们将从 Todo MVC 停止的地方开始，为允许跨设备同步 Todo 的应用程序构建一个后端。我们将看看一个特定的云提供商 [Azure](https://azure.microsoft.com/en-us?WT.mc_id=devto-blog-rylevick) 内部的不同可能性，但希望最终其他人可以为其他云提供商添加类似的教程。

我们开始吧！

## 前端

我们将从一个完整的前端 TodoMVC 应用程序开始。如果你有一个喜欢的框架，可以随意使用在[todomvc.com](http://todomvc.com)上找到的 TodoMVC 应用。在我们的例子中，我们将使用一个稍微精简的 Vue.js 版本。你可以在这里找到代码。按照自述文件中的说明，随意克隆 repo 并在本地运行应用程序。

如果你对 Vue.js 不熟悉，可以从[官网](https://vuejs.org)上的牛逼资源中了解更多。我们不需要修改太多的应用程序，使其与我们的 API 一起工作，所以如果你以前没有使用过 Vue，请不要担心。

## 创建一个 API

我们要做的第一件事是创建一个非常简单的 API 来创建 todos。为了简单起见，我们将使用 Todo MVC 中使用的相同语言 JavaScript 作为后端。对于如何构建我们的 API，我们有几种选择，每一种都有其优缺点。例如，我们可以使用像 [Express](https://expressjs.com) 这样的框架，在我们必须自己管理的虚拟机(VM)上运行应用程序。或者我们可以让 Azure 为我们做更多的工作，并在 [Azure 应用服务](https://azure.microsoft.com/en-us/services/app-service?WT.mc_id=devto-blog-rylevick)上运行 API，该服务处理供应，甚至在看到大量流量时扩展我们的应用。

但是，由于我们刚刚开始，我们希望事情简单——我们必须做的工作越少越好。出于这个原因，我们将使用 Azure 的功能即服务(FaaS)产品 Azure Functions。有了函数，我们可以编写处理 HTTP 请求的代码，并将其部署到云中，而不用担心我们想要运行多少服务器，它们是否安装了正确的依赖项等等。我们只需编写代码，服务将确保它正常运行。

FaaS 通常也被称为无服务器，但这个名字有点误导。服务器还是有的，我们只是不用担心管理它们；我们只需要写代码，剩下的就交给 is 了。

我们将从创建一个创建 todos 的函数开始。

## 创建功能 App

我们需要做的第一件事是创建一个“功能应用程序”。各种功能总是被组合在一个“应用程序”下。起初，我们会有一个函数(一个用于创建 todo)，但最终我们会有更多的函数。这允许我们将这些功能组合成一个逻辑组。

有很多方法可以创建一个 Azure Function 应用程序，并用各种功能填充这个应用程序，包括从 Azure 门户、VS 代码和命令行。如果你想了解更多，我已经在之前[写过这个。](https://dev.to/azure/the-most-minimal-serverless-function-3cm1)

我创建 Azure 函数的首选方式是使用 [Azure Functions 命令行工具](https://github.com/Azure/azure-functions-core-tools?WT.mc_id=devto-blog-rylevick)(我们可以在装有 brew `brew tap azure/functions; brew install azure-functions-core-tools`的 macOS 和装有 npm `npm i -g azure-functions-core-tools --unsafe-perm true`的 Windows 上轻松安装)。

要创建一个新的 Azure Functions 应用程序，运行下面的代码，选择`node`作为运行时，选择`JavaScript`作为语言:

```
$ func init 
```

然后通过运行下面的代码并选择`HTTP trigger`并将函数命名为`CreateTodo`来创建一个 Azure 函数。

```
$ func new 
```

我们现在将函数放在一个`CreateTodo`目录中。我们可以通过运行下面的命令在本地运行它:

```
# make sure you have an LTS version of node like node 10.15.3
func start 
```

如果您在浏览器中访问`http://localhost:7071/api/CreateTodo?name=World`，您应该会看到“Hello World”。

## 储存

在开始编写函数之前，我们需要决定在哪里存储数据。同样，我们希望选择一些简单的东西，使我们能够有效地读取或写入数据，而不需要太多的麻烦。虽然我们可以使用 SQL 数据库或其他一些更强大的选项，但目前最好的选项是一个简单的键/值存储，它允许我们向其中写入数据，而不必事先定义结构。

为此，我们将选择 [Azure Table Storage](https://docs.microsoft.com/en-us/azure/cosmos-db/table-storage-overview?WT.mc_id=devto-blog-rylevick) ，它像其他键/值存储(例如 Redis)一样，为我们提供了一种简单的方式来写入将在某个键上分区的数据，从而实现高效的查找。在 todo 应用程序中，每个用户都有一个 todo 列表，最直接的答案是使用用户的标识符作为键。在我们的例子中，因为我们还不关心身份验证，所以我们只需要用户提供一个电子邮件地址，我们将使用它作为密钥来对数据进行分区。这样，当我们想要获取所有用户的待办事项时，我们只需要提供带有他们电子邮件地址的表存储，它将能够非常快速地获取待办事项。

好了，说得够多了，让我们开始工作吧。

## 添加表存储访问功能

我们首先需要配置我们的函数，以便能够与表存储对话。这需要扩展 Azure Functions 运行时，因为默认情况下它只支持 HTTP 请求处理。

我们可以用 Azure Functions CLI 工具做到这一点。要围绕扩展系统设置锅炉板，运行以下程序:

```
$ func extensions install 
```

除此之外，这会创建一个`extensions.csproj`文件。如果你以前从未做过 dotnet 开发，那么`csproj`文件只是用 xml 编写的 dotnet 项目的项目定义文件。函数运行时是用 dotnet 编写的，所以为了扩展它，我们需要编辑一个`csproj`文件。

在`extensions.csproj`文件中，我们需要将下面一行添加到`ItemGroup`列表中:

```
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" /> 
```

这表明我们希望使用存储扩展，它允许我们在其他类型的存储服务(如 blobs 和文件)中使用表存储。

我们需要再次运行`func extensions install`来安装这个特定扩展的所有依赖项。

## 配置我们的功能来使用表存储

好了，现在我们可以在我们的函数应用程序中使用存储扩展了，我们需要配置我们的`CreateTodo`函数来实际使用表存储。

在`CreateTodo`目录中，有一个`function.json`文件，它是这个特定功能的配置。它应包含以下内容:

```
{  "bindings":  [  {  "authLevel":  "function",  "type":  "httpTrigger",  "direction":  "in",  "name":  "req",  "methods":  [  "post"  ]  },  {  "type":  "http",  "direction":  "out",  "name":  "res"  },  ]  } 
```

`bindings`值让我们的函数知道它是如何绑定到外部世界的。在这种情况下，我们让函数知道它将被一个我们称之为`req`的 http POST 请求触发，它将返回一个我们称之为`res`的 http 响应。我们的函数可以被其他东西触发，比如定时器和事件，但是这个函数是为 http 配置的。

我们需要为表存储添加一个额外的绑定。将以下内容添加到`"bindings"`数组:

```
{  "type":  "table",  "tableName":  "Todos",  "direction":  "out",  "name":  "todosTable",  "connection":  "STORAGE_CONNECTION"  } 
```

`type`字段表示我们正在为一个表创建一个绑定，`tableName`表示我们正在与一个名为`Todos`的表交互(我们将很快创建这个表)，`direction`表示我们正在将`out`写入这个表，而不是从这个表中读取`in`，`name`是我们将在函数代码中用来引用我们的表的名称，`connection`是环境变量，我们将在这个变量中存储关于如何连接到我们的存储帐户的信息，比如帐户名和帐户密钥。

## 与表存储对话

现在我们已经配置了函数来使用表存储，我们需要实际使用它。在`CreateTodo`目录中，有一个包含我们函数代码的`index.js`文件。将其内容替换为:

```
module.exports = async function (context, req) {
  // check for an email inside of the Authorization header
  const email = req.headers["authorization"]
  if (!req.body || !email) {
    // there was no body or no Authorization header
    // setting context.res will make our function return the specified response
    context.res = {
        status: 400,
        body: "missing body or authorization header"
    }
  }

  const todo = {
    id: generateId(),
    title: req.body.title
  }

  if (!validTodo(todo)) {
    context.res = {
      status: 400,
      body: "invalid todo"
    }
    return 
  }

  // the todo is valid
  // write to the todosTable
  context.bindings.todosTable = [{
    PartitionKey: email,
    RowKey: todo.id,
    title: todo.title
  }]

  context.res = {
    // the response is a 200 by default
    body: todo
  }
}

function validTodo(todo) {
  return todo && todo.title
}

// https://stackoverflow.com/questions/105034/create-guid-uuid-in-javascript
function generateId() {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, c => {
    const r = Math.random() * 16 | 0
    const v = c == 'x' ? r : (r & 0x3 | 0x8)
    return v.toString(16)
  })
} 
```

函数要求我们导出一个带两个参数的异步函数:一个代表函数上下文的`context`对象和一个代表当前 http 请求的`req`对象。

我们的函数只是检查我们是否有一个`Authorization`头(我们使用全部小写字母访问它，并假设它是一个电子邮件地址)和一个请求体。如果没有，我们返回一个 http `400`响应。

然后我们创建一个`todo`对象，在请求体中传递给我们一个生成的 id 和一个标题。然后我们检查以确保 todo 是有效的(通过检查标题是真实的)。

一旦我们知道我们有一个有效的`todo`，我们就可以将`context.bindings.todosTable`设置为一个对象数组，这些对象将存储在我们的表中。记住，我们在`functions.json`文件中将`todosTable`设置为表绑定的名称。

我们放在分配给`todosTable`的数组中的对象有三个键:

*   我们的表将如何分区。表存储可以在分区上进行非常快速的查找，因此我们可以通过电子邮件进行分区，这样当我们想要通过电子邮件查找待办事项时，表存储可以快速返回给我们所有与该电子邮件相关的待办事项。
*   `RowKey`是分区内的唯一标识符。我们将使用我们生成的 id。这些 id 是全球唯一的，甚至比`RowKey`更唯一。
*   `title`只是我们 todo 的标题。表存储要求我们提供`PartitionKey`和`RowKey`，我们可以添加任意多的额外字段。目前，我们只是存储`title`。

## 创建我们的表存储

为了让我们成功运行这个功能，我们需要实际创建一个 Azure 存储帐户，以便我们可以使用表存储。要做到这一点，我们将使用 [Azure CLI](https://github.com/Azure/azure-cli) ，你可以按照这里的指导[安装](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest&WT.mc_id=devto-blog-rylevick)。

首先，你需要确保你已经登录到 Azure，这可以通过运行`az login`来完成。

然后，我们将创建一个资源组，所有的 Todo 云资源都将驻留在这个资源组中。资源组只是对资源进行分组的一种方式，因此您可以一起处理它们。运行以下命令:

```
az group create \
    --name TodoCloud \
    --location eastus 
```

随意选择离你更近的[地点](https://azure.microsoft.com/en-us/global-infrastructure/locations?WT.mc_id=devto-blog-rylevick)。也可以通过运行`az account list-locations --query "[].name"`查看所有位置。

接下来我们将[创建账户](https://docs.microsoft.com/en-us/cli/azure/storage/account?view=azure-cli-latest&WT.mc_id=devto-blog-rylevick) :

```
$ az storage account create \
    --name todocloud \
    --resource-group TodoCloud \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob 
```

您可以将`--location`更改到离您最近的位置。其余的保持原样就可以了。

我们就快成功了！我们现在有了一个存储帐户，我们可以使用下面的命令创建我们的`Todos`表:

```
$ az storage table create --account-name todocloud --name Todos 
```

我们现在已经为我们的功能做好了一切准备。我们需要做的最后一件事是确保我们在`functions.json`配置文件中使用的`STORAGE_CONNECTION`环境变量被正确填充。我们可以通过在我们的函数应用程序的`local.settings.json`文件中的`Values`键中设置它来实现。这些值被自动设置为我们的函数应用程序可以使用的环境变量。

在您的`local.settings.json`文件的`Values`数组中添加以下键和值:

```
"STORAGE_CONNECTION":  "DefaultEndpointsProtocol=https;AccountName=todocloud;AccountKey=$SOME_LONG_KEY" 
```

确保用正确的帐户密钥替换`$SOME_LONG_KEY`。您可以使用以下命令找到存储帐户密钥:

```
$ az storage account keys list \
    --account-name todocloud \
    --resource-group TodoCloud
    --query "[0].value" 
```

有了这些，我们现在应该能够使用下面的命令在本地再次运行我们的函数应用程序:

```
$ func start 
```

要创建 todo，我们可以使用 cURL:

```
curl -XPOST http://localhost:7071/api/CreateTodo -d '{"title": "Buy milk"}' -i -H "Authorization: ryan.levick@example.com" 
```

如果你得到一个 200 的返回，你可以确定你的 todo 已经存储在表存储中。

你可以使用 [Azure Storage Explorer 应用](https://azure.microsoft.com/en-us/features/storage-explorer?WT.mc_id=devto-blog-rylevick)来查看你的表中的数据。

## 改变我们的 TodoMVC app

在我们的客户端代码`app.js`中，我们将想要替换`addTodo`方法(当用户在键入 todo 以创建它之后点击 enter 键时调用该方法)。
将现有的`addTodo`机构替换为:

```
const value = this.newTodo && this.newTodo.trim()
if (!value) {
  return
}
fetch(API + "/CreateTodo", {
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json',
    "Authorization": "ryan.levick@example.com"
  },
  method: "POST",
  body: JSON.stringify({ title: value })
}).then(res => {
  if (res.status < 500) {
    this.todos.push({ id: this.todos.length + 1, title: value, completed: false })
    this.newTodo = ''
  }
}) 
```

这段代码很简单，也不是最好的用户体验，但是它确实有用。就像之前一样，我们从`this.newTodo`获取 todo，并对其进行一些清理。但是我们没有向本地运行的 API 发出 http POST 请求。如果响应的状态小于 500，我们像以前一样将 todo 保存到本地状态，并重置`newTodo`输入字段。

如果我们在客户端的路径上打开`index.html`文件，并添加一个 todo，我们将得到一个 CORS 错误。为了让我们的应用程序支持 CORS 通话，我们必须做最后一项更改。我们需要在值为`{"CORS": "*"}`的`local.settings.json`文件中为 JSON 对象添加一个`Host`键。

如果我们重新启动本地运行的函数，并尝试创建一个 todo，它应该可以工作。

万岁！🎉

## 结论

在这篇文章中，我们做了很多，我们创建了一个 Azure 函数，并将其与 Azure 上的存储帐户中新创建的表连接起来。

在下一篇文章中，我们将创建一个“GetTodos”端点来获取我们的 Todos，我们将把我们的功能部署到 Azure，这样我们就不需要在本地运行它了。

我很乐意看到其他人如何在 Azure 和其他云提供商上为 TodoMVC 创建后端。请随意为回购做一个公关，写一篇关于你的经历的博客，或者在 T2 的推特上联系我。