# TypeScript Azure 函数的类型化绑定

> 原文：<https://dev.to/azure/typed-bindings-for-typescript-azure-functions-4k3f>

几周前，微软宣布了他们对 [TypeScript Azure Functions](https://azure.microsoft.com/en-us/blog/improving-the-typescript-support-in-azure-functions/?WT.mc_id=devto-blog-aapowell) 的改进，用一些新的模板来帮助你入门。

由于我目前正在用 Azure 函数做一些东西，所以我决定试一试，并分享我的一些心得。今天我想谈谈如何用 TypeScript 提高 Azure 函数的类型化。

有了 TypeScript，自然还有 JavaScript，我们依靠 [`function.json`](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference?WT.mc_id=devto-blog-aapowell) 文件来创建我们对不同服务的绑定(因为我们没有像 NET 函数可以利用)。但是这导致了我们正在绑定的内容和我们的编辑器所了解的内容之间的脱节。

一个标准的 [HTTP 触发器](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-http-webhook?WT.mc_id=devto-blog-aapowell)绑定将看到一个文件，在 TypeScript 中是这样搭建的:

```
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    // function code here
}

export default httpTrigger; 
```

这里我们依赖于 Functions TypeScript 包提供的一些基本类型，但是它根本不理解我们的应用程序。

## 扩展内置接口

为了改进这一点，我已经开始扩展`@azure/functions`包中提供的内置接口来理解我正在创建的绑定，就像这样:

```
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

interface InputHttpRequest extends HttpRequest {
    query: {
        name: string
    }
}

const httpTrigger: AzureFunction = async function (context: Context, req: InputHttpRequest): Promise<void> {
    const name = req.query.name;

    // function body
};

export default httpTrigger; 
```

对于这个例子，不是让`req.query`具有类型`[key: string]: string`，这意味着它是一个*任何东西*的字典，我是说我期望所提供的查询字符串将`name`作为一个值(也可能是其他值，但是我只关心`name`)。这给了我很好的代码完成，我期望我的类型是什么样子，当我创建测试时，我也知道了对象的形状。

## 打字绑定

假设您的函数有两个额外的绑定，一个队列输出和 HTTP 响应输出。同样，我们可以扩展内置类型来实现这一点，这次我们将扩展`Context`。

这是我们`function.json`的绑定:

```
 {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueue",
      "queueName": "my-queue",
      "connection": "QueueConnectionString"
    } 
```

打字稿是:

```
interface InputFunctionContext extends Context {
    bindings: {
        myQueue: string[]
    }

    res: {
        status?: number
        body: string
    }
}

const httpTrigger: AzureFunction = async function (context: InputFunctionContext, req: InputHttpRequest): Promise<void> {
    // function code
} 
```

`bindings`和`res`都有一个默认类型`{ [key: string] : any }`,表示它们可以有尽可能多的属性，并且它们是无类型的，但是我们从`function.json`中知道它们应该是什么，我们可以相应地设置它们。

您可以对输入绑定(如 Table)做同样的事情，并将它们输入到类中，它们通常在您的应用程序中。

## 结论

从使用 TypeScript Azure 函数的非常松散的类型设计开始，您可以轻松地利用类型扩展来使您的函数代码更加了解绑定以及它们应该表示的类型。

我在 GitHub 上创建了一个[完整的工作示例，如果你想自己玩的话。](https://github.com/aaronpowell/typed-typescript-functions-demo)