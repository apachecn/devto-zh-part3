# 创建第一个具有持久功能的无服务器工作流

> 原文：<https://dev.to/azure/durable-functions-stateful-long-running-functions-in-serverless-part-i-5bm>

在 [Twitter](https://twitter.com/chris_noring) 上关注我，很乐意接受您对主题或改进的建议/Chris

> 持久函数是 Azure 函数的扩展，它允许你在无服务器环境中编写有状态函数。持久功能为您管理`state`、`checkpoints`和`restarts`。

你问这话是什么意思？

这意味着你可以拥有长时间运行的函数，比如真正的长时间运行的函数。它也有一个状态，这意味着它记得它在哪里，就像一个工作流。

> 好吗？

这个怎么样？想象一下你有这样一种情况，你需要通过将某样东西划分到不同的检查点来管理它。每个检查点都离被认为是*处理的*更近了一步。更具体地想象一个游戏，例如，你需要加载一堆不同的资源，只有当所有的东西都被加载并准备好了，你才能玩这个游戏。

> 哦，好的，这就像一个工作流程框架

是的，正是如此，它使您能够指定应该如何通过流程来执行某件事情。甚至有不同的架构模式推荐给不同的流程。

> 听起来很贵，是吗？

不，不完全是，付费模式非常接近 Azure Functions 使用的模式，只在功能/工作流实际执行时付费。

> 听起来不错，告诉我更多

在本文中，我们将涵盖:

*   **什么是持久功能**，我们来讨论一下它是什么，核心概念是什么
*   **它是如何工作的**，我们将解释一点它是如何工作的资源，我们将提供一些资源，以便您可以进一步深入研究
*   **Lab** ，我们将通过一个例子进行编码，这样你就可以看到使用中的主要概念以及当

## 概念和高层解释

在处理持久函数时，我们需要了解一些概念。所有的概念都扮演着一个角色，共同使我们能够运行我们的持久功能。

*   **Orchestrator 功能**，这是一个我们定义工作流的功能，我们设置工作流中应该发生的事情、要执行的活动以及完成后发生的事情
*   **活动功能**，活动功能是持久功能编排中的基本*工作单元*。活动功能是流程中编排的功能和任务。您可以拥有任意多的活动功能。确保给它们起描述性的名字，代表你的流程中的步骤
*   **客户端功能**，客户端功能是创建编排的新实例的触发功能。客户端功能是创建持久功能编排实例的*入口点*

> 好的，我想我明白了，但是你能再解释一下吗？

当然，最好的解释方式是通过一个现实的例子和一幅图像。那么我们来说说*订单处理*。在*订单处理*中，我们设想要执行以下任务:

*   **盘点**盘点、
*   **收费**客户、
*   **创建**一个装运

鉴于我们知道订单是如何处理的，让我们向您展示这张图片，以便您对工作流程有所了解:

[![](img/fd2824b5b01dd425f00cb252d985def1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GxjDEGeE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yv14fujavcff87q9rq0w.png)

好了，上面我们看到了一个客户端函数是如何被调用的。在创建订单的情况下，这通常是我们从应用程序中点击的 HTTP 端点。接下来要发生的事情是，客户端函数启动一个编排实例。这意味着我们将获得一个`instance id`，我们对那个特定流的唯一引用。接下来要发生的事情是，我们尝试在流程编排中执行所有事情，比如`checking the inventory`、`charging the customer`和`creating a shipment`。

### 工作原理

让我们更详细地讨论一下这在技术上是如何工作的。编排的问题在于，它编排的内容通常是异步的，这意味着我们不知道某件事情的确切完成时间。为了避免您为其支付运行成本，持久功能会关闭并保存状态。

当一个编排功能被赋予更多的工作要做时(例如，接收到一个响应消息或一个持久定时器到期)，该编排器醒来并从头重新执行整个功能以重建本地状态。

> 等等，重新运行一切？

不用担心，在重放期间，如果代码试图调用一个函数(或做任何其他异步工作)，持久任务框架会查询当前编排的执行历史。如果发现 activity 函数已经执行并产生了结果，它会重放该函数的结果，orchestrator 代码会继续运行。

> 哦，好的，听起来好多了

重放会一直继续，直到函数代码完成或者它已经调度了新的异步工作

## 资源

*   免费帐户 Azure 帐户你需要在 Azure 上注册才能使用持久功能
*   [使用 JavaScript 创建您的第一个持久函数](https://docs.microsoft.com/en-us/azure/azure-functions/durable/quickstart-js-vscode?wt.mc_id=devto-blog-chnoring)快速入门，带您创建一个持久函数
*   [持久功能概念](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-concepts?wt.mc_id=devto-blog-chnoring)在这里阅读更多关于概念和模式以及如何实现这些模式的内容。
*   [Orchestrator 功能约束](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-checkpointing-and-replay#orchestrator-code-constraints?wt.mc_id=devto-blog-chnoring)您需要了解的约束。

## 实验室-简单活动流程

我们相信最好的学习方法是用它来建立一些东西。那么我们该怎么做呢？嗯，很简单。使用 VS 代码，我们可以安装一个插件，让这个过程变得非常简单。

[![](img/9ab433e8686c13d03113bfdf1faf559e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4ml9XNYa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z6twu7tn3q3yyvejy6ug.png)

### 创建我们的项目

打开命令面板或键入`COMMAND + SHIFT + P`。

然后，我们选择以下选项，创建一个新项目

[![](img/827ea9da6b08273230a71d947b5ad675.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x_LFpvbJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/07e3b91mhymqih0kd51z.png)

接下来是我们选择一种语言，我们来看看`javascript`。然后我们面临着一系列的选择:

[![](img/4720a4c77b4e3da90587aa9f21e38302.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H4ZrLcV---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ohrjmvtyir344luhu4tq.png)

正如您在上面看到的，我们突出显示了三个选项，因为这些是我们将在整个实验中使用的选项。我们需要一个入口点`Durable Functions HTTP Start`,所以让我们先选择它:

[![](img/e4495d2b40f3ab35bb27c398ba56ecfc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--veDecxjg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jd1fs7ygct5x6yg7hdyz.png)

接下来，让我们再次创建我们的 orchestrator 函数 os，点击`COMMAND + SHIFT + P`并选择`Azure Functions: Create Function`和`Durable Functions Orchestrator`，让我们把它命名为`Orchestrator`。

这样做时，将要求您选择一个存储帐户:

[![](img/a52b5cd997785fca76c1ae471c6ec4fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XdbMgBIo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/avyj4jd7ul7eh28tcq81.png)

您需要选择`Subscription`、`Storage account`和`Resource group`。这样做的原因是，当您保存函数的状态时，需要将它保存在某个地方，以便以后恢复。

我们就快成功了，只差一件事来创建`Activity function`。我们也可以用`COMMAND+SHIFT+P`、`Azure Functions: Create Function`和`Durable functions activity`来创建它，当它要求我们给它命名时，我们就给它命名为`Hello`。

如果你跟着它走，它应该是这样的:

[![](img/81e5a14dc77fb9d6ba5a6253ec08c20a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cM31CKH6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m2kb428g903i79r3hab0.png)

### 解释神器

好了，我们创建了三个不同的工件，一个 *orchestrator* 函数，一个 *HTTP start/client 函数*和一个 *activity 函数*。这一切是如何运作的？

嗯，这一切都是从启动一切的`HttpStart`函数开始的。然后，所述函数启动`Orchestrator`，T1 又启动编排器中指定的`Activity functions`。听起来有点理论化，但是让我们深入这些工件，看看代码中发生了什么。

#### HttpStart

如上所述，这是启动这一切的函数。让我们看看它的源代码并讨论发生了什么:

```
// HttpStart/index.js

const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = await client.startNew(req.params.functionName, undefined, req.body);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
}; 
```

上面我们可以看到，我们通过调用从库`durable-functions`导入的持久函数实例`df`上的`getClient`来获取对`client`的引用。
接下来，我们的`client`实例调用`startNew`，产生一个`instanceId`。`instanceId`是对这个特定函数调用的引用或*处理程序*。这对于本演示来说并不重要，但对于第二个演示，我们将使用该信息。
最后要做的事情是我们创建一个 HTTP 响应。

让我们看一下`function.json`，我们的配置文件，在这里我们为我们的函数设置输入和输出:

HttpStart/function.json

```
{  "bindings":  [  {  "authLevel":  "function",  "name":  "req",  "type":  "httpTrigger",  "direction":  "in",  "route":  "orchestrators/{functionName}",  "methods":  [  "post",  "get"  ]  },  {  "name":  "$return",  "type":  "http",  "direction":  "out"  },  {  "name":  "starter",  "type":  "orchestrationClient",  "direction":  "in"  }  ]  } 
```

我们有两条有趣的信息。首先，我们有一个`httpTrigger`，也就是说，我们可以通过一个 HTTP 调用，特别是通过一个名为`orchestrators/{functionName}`的路由来访问这个函数。另一条有趣的信息是最后一个类型为`orchestrationClient`的条目。这使我们能够在代码中获得一个`client`引用，没有它我们就不能。因此，如果您需要一个客户机实例，请记住包含这个配置。

#### 管弦乐手

接下来让我们看看管弦乐队。这是所有有趣的事情发生的地方，这是我们建立流程的地方，什么时候调用什么函数以及为什么调用。我们来看代码:

```
// Orchestrator/index.js

const df = require("durable-functions");

module.exports = df.orchestrator(function* (context) {
    const outputs = [];

    // Replace "Hello" with the name of your Durable Activity Function.
    outputs.push(yield context.df.callActivity("Hello", "Tokyo"));
    outputs.push(yield context.df.callActivity("Hello", "Seattle"));
    outputs.push(yield context.df.callActivity("Hello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}); 
```

首先我们看到我们有一个方法`orchestrator`,它采用了一个生成器函数。什么是发电机？它们是与`async/await`非常相似的概念，允许你以一种看起来同步的方式执行异步代码。你可以通过将`*`作为函数声明的一部分来识别它们，比如:

```
function*() {} 
```

并且使用了关键字`yield`。`yield`的用法和`await`一样，意味着我们应该留在这里，在代码中等待，直到我们的异步操作结束。

那么这对我们上面的代码意味着什么呢？我们再仔细看看:

```
 outputs.push(yield context.df.callActivity("Hello", "Tokyo")); 
```

这里我们可以看到，我们用参数`Hello`调用了`context.df.callActivity()`，用关键字`yield`调用了`Tokyo`。这仅仅意味着我们正在调用一个带有参数`Tokyo`的活动函数`Hello`。我们可以看到，还有两个对`callActivity()`的调用在我们的活动函数结束之前不会执行。

#### 您好

接下来我们有一个活动函数。这是我们进行所有繁重工作的地方。查看`Hello`目录的`index.js`，我们看到下面的代码:

```
// Hello/index.js

module.exports = async function (context) {
    return `Hello ${context.bindings.name}!`;
}; 
```

我们看到它会立即返回，但它肯定会是一个长期运行的活动。关键是它是在一毫秒内运行还是需要一些时间，这都不重要，编排功能仍然要等待它结束。

### 调试

到目前为止，你可能认为你已经理解了所有的东西，但是当你看到调试流程发生时，你真的明白了。这就是我们接下来要做的，我们将从 VS 代码中启动我们的持久函数，您将能够看到断点是如何命中的以及何时命中的。

我们需要做的第一件事是安装我们一直提到的`durable-functions` NPM 库，所以让我们开始吧:

```
npm install durable-functions 
```

现在我们已经准备好调试，让我们点击调试按钮。

我们应该在终端中打印出这样的内容

[![](img/2cc9363a8263350d45285de33e0e8eaa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ltKMXmTa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v6g5rkpkskgp3v12tayv.png)

下一件我们需要做的事情是通过点击我们的客户端函数路径来启动一切，如上所述`orchestrators/{functionName}`。因为我们只有一个这样的名为`Orchestrator`的函数，所以我们需要在浏览器中调用下面的 URL 来启动整个过程:

```
http://localhost:7071/api/orchestrators/Orchestrator 
```

首先发生的是我们的`HttpStart`函数和它的`index.js`函数被这样攻击:

[![](img/89a111a98be50f064741bdb6050038c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GFRBTpJ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/93zojxsk69kct56i2qum.png)

我们让调试器前进:

[![](img/e8e2b9d053179b408741693a2c64fcea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O21mY70q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ex54yfi4o7tyey4b1xwg.png)

上面我们看到了`Orchestrator`和它的`index.js`是如何被击中的。

接下来我们前进到下一个断点，我们看到我们的活动函数`Hello`和它的`index.js`下一次被点击。

[![](img/5935f3e17e2bd3884f3f916d4b8ef8ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uSYvnaMd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vgvgi1lwlww6mhbidlm8.png)

我们推进了断点，发现自己又回到了编排功能中:

[![](img/651dc30a54f5b270e9f14e05e7607a73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J44jO_FJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/47nty0r9iz8b609fzcil.png)

这将导致再次点击`activity`函数，这次使用参数`Seattle`，如下所示:

[![](img/05e7dcce914826e25a98a8a1d1a8e4b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N9WSFjSX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r2qo8squak483rokwuet.png)

如您所见，在活动函数和 orchestrator 之间会一直这样，直到 orchestrator 完成。

让我们前进通过所有的断点。

我们最终来到这样一个页面，这是来自名为`HttpStart`的方法的 HTTP 响应

[![](img/8a5802f8a3376041e8ed697273dee6a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HzKxxYME--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z2w5665kgu0lwdd9jacp.png)

我们现在想知道的有趣的是，我们最终生产了什么？答案就在那个叫`statusQueryGetUri`的网址里。让我们跟随链接:

[![](img/3432757d9bcb6e76ba9a86ccd0e2a1e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---zEDqKv4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1o37k5qdllc93kmvku9j.png)

正如你在上面看到的，我们的`Orchestration`函数的响应是一个由所有活动函数的响应组成的数组，比如:

```
"output":  [  "Hello Tokyo!",  "Hello Seattle!",  "Hello London"  ] 
```

因为我们构造代码的方式，它以那种方式结束，我们写了这个:

```
outputs.push(yield context.df.callActivity("Hello", "Tokyo"));
outputs.push(yield context.df.callActivity("Hello", "Seattle"));
outputs.push(yield context.df.callActivity("Hello", "London"));

// returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
return outputs; 
```

## 总结

关于持久函数还有很多东西要学，但是我已经听到你们中的一些人在这一点上打鼾了，这就是为什么我们将应用程序模式和模式实现等主题留到下一部分。

所以我希望你对此感到兴奋。

### 鸣谢

如果没有您在持久函数如何工作方面的指导，我不会写这篇文章。你们两个都是了不起的人。

所以给他们一个关注，他们真的知道他们的无服务器的东西

*   Anthony Chu [dev.to](https://dev.to/anthony) ， [twitter](https://twitter.com/nthonyChu)
*   杰里米·利克内斯[开发到](https://dev.to/jeremylikness)推特