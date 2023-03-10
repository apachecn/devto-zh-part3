# 节点环境变量:简单的例子

> 原文：<https://dev.to/stephencweiss/node-environmental-variables-a-simple-example-202e>

这不是对 Node 的环境变量的深入探究，而是对如何使用环境变量的快速浏览。

我想测试应用程序的负载平衡。这意味着我需要一种方法来动态设置我的服务器将监听的端口。

事实证明，这是如何使用从命令行提供的环境变量的一个很好的例子。

然后可以从应用程序中引用这些来设置变量——在我的例子中是`port`。

# 示例:在多个端口上运行同一应用的多个实例

在我的服务器文件`index.js`中，我将我的端口定义为:`const port = process.argv[2] || 8081`。

这意味着如果我无法通过一个显式端口，我将使用默认的 8081。

但是，我也可以在节点(process.argv[0])后传入一个变量*，文件节点正在运行(process.argv[1])，它将被映射到我的端口变量。*

这是一个简单的用例。这也是我遇到的第一个用例，它帮助我了解了流程参数在 Node.js 中是如何工作的。

# 附加资源

*   [流程| Node.js v11.3.0 文档](https://nodejs.org/docs/latest/api/process.html#process_process_argv)
*   [process.env:它是什么，为什么/何时/如何有效地使用它](https://codeburst.io/process-env-what-it-is-and-why-when-how-to-use-it-effectively-505d0b2831e7)