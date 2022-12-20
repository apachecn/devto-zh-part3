# 经验教训—生产中“完全无服务器”的一年

> 原文：<https://dev.to/ketacode/lessons-learneda-year-of-going-fully-serverless-in-production-3mh8>

# 经验教训——生产“完全无服务器化”的一年

照片由亚伦·伯顿拍摄

在[鸟居](https://toriihq.com)，我们决定尽可能走**无运营**的道路，这意味着我们将把所有精力集中在我们的产品上，而不是在**运营**。虽然我们喜欢开发运维，但这不是我们公司的主要关注点。

> ***无服务器，因为我们喜欢晚上睡觉。*T3】**

我们可以将应用程序分为三个部分:

1.  **静态网站**。这些是前端网站，用 React 编写，在构建时静态生成。

2.  **后台工作。**这些是由文件上传、webhooks 或任何其他异步事件等事件安排或触发的作业。

3.  **API 服务器。* * REST API 服务器与我们的数据库进行交互，并为所有客户端请求提供服务。

## 吸取教训

### **#1。静态网站**

静态网站速度快、易于扩展且易于分发。我们使用 React 构建我们的前端，代码被打包成一个简单的 HTML/JS/resources 包，准备分发。

我们使用 [Netlify](https://www.netlify.com) 在 CDN 上托管这些静态资产，并从世界任何地方获得**快速加载时间**。

这里没有要配置的 Nginx/Apache 服务器👍

### **#2。无服务器上的 API 服务器**

基本思想是 API 服务器是一个函数:输入是一个 HTTP 请求，输出是一个 HTTP 响应。对于 FaaS 来说**是完美的** ( *功能即服务*)，每个 HTTP 请求都有自己的服务器实例来处理。

**这种设置带来了自动可扩展性、高可用性，并显著降低了成本**。它还使事情变得更简单，因为移动的部分更少:没有服务器，没有负载平衡器，没有自动扩展组。所有这些都被抽象掉了，我们只关心一个函数。

我们将整个 Node.js 应用程序打包成一个单独的 AWS Lambda 函数。API 网关将所有流量路由到它，Node.js 应用程序将其视为常规 HTTP 请求。

我们选择 apex/up 来设置堆栈、更新它和部署我们的功能。这真的就像在你的终端里写一样简单。它是高度可配置的，所以您可以根据自己的需要定制部署，但是如果您没有特殊的需求，那么默认的部署就可以了。

**无需为任何服务器供应、配置或应用安全补丁👏**

### **#3。无服务器包装**

部署 Lambda 函数有一个 52Mb 的函数限制，包括它的所有依赖项。如果你最近编写了一个不错的 Node.js 项目，你就会知道我们可以轻松地超越这个限制。注意:有一种从 S3 部署它的方法可以绕过这个限制，我们还没有尝试过。

为了减轻这种情况，我们只包含必需的依赖项，并通过排除未使用的文件(如 READMEs、包历史、测试、文档和示例)来缩减它们的大小。我们发布了一个名为 [lambdapack](https://www.npmjs.com/package/lambdapack) 的包来帮助完成这项工作。它将使用 webpack 打包您的代码，以提供最新的 Node.js 和 JavaScript 特性，同时保持您的 node_modules 尽可能小。lambdapack 与 apex/up 完全集成，因此构建过程得到优化并高效打包。

在 GitHub 上阅读更多关于 [lambdapack](https://www.npmjs.com/package/lambdapack) 的信息。

### **#4。部署**

这非常有效，每个部署都创建了 Lambda 的一个新版本。AWS 允许保存每个 Lambda 的多个版本，并有指向版本的别名。流行的别名包括:测试、试运行和生产。因此，新的部署意味着上传 Lambda 的新版本，并将生产别名指向它。幸运的是，up 通过 up deploy production 自动完成这项工作。回滚只是将指针别名化为所需的版本。

### **#5。本地测试/开发**

因为我们使用的是常规的 Node.js 服务器，所以本地运行就意味着像往常一样运行您的服务器。然而，这并没有模仿 AWS 基础设施的所有重要差异，例如:实施相同的 Node.js 版本、API 网关超时、Lambda 超时、与其他 AWS 资源通信等等。不幸的是，最好的测试方式是在 AWS 基础设施本身上。

### **#6。后台工作**

对于后台作业，如文件处理或与第三方 API 同步，我们保留了一组不属于 API 服务器的专用 Lambda 函数。这些作业被安排由 CloudWatch 运行，或者作为对我们系统中事件的响应。

目前我们使用一个“兄弟”项目来处理这些后台工作 Lambdas——使用开源的 [apex/apex](https://github.com/apex/apex) 。

这些功能只在需要时运行，不需要让服务器一直运行来处理这些任务。无服务器方法的另一个胜利🚀

### **#7。记录**

AWS 服务附带了内置的 CloudWatch 日志服务，它有糟糕的用户界面、UX 和 DX。虽然 up cli 具有查看日志的日志功能，但还有很多问题需要解决:警报、聚合日志等。

我们的第一个解决方案是直接从 API 服务器记录到第三方记录服务(我们使用 [papertrail](https://papertrailapp.com) ),但是这保持了 Lambda 函数始终运行。

**一个更好的方法是将 Lambda 日志**流式传输到一个专用的 Lambda 中，该专用的 Lambda 负责将其发送给第三方日志服务。我们使用了升级版的 [cloudwatch-to-papertrail](https://github.com/apiaryio/cloudwatch-to-papertrail) 我还建议流式传输 API 网关日志以获得完整的图片。

### **#8。环境变量和秘密**

不要将你的秘密提交给源代码控制。既然我们已经解决了这个问题，我们应该将它们加密存储在某个地方。AWS 有一个解决方案，它被称为 [AWS 参数存储](https://aws.amazon.com/ec2/systems-manager/parameter-store/)。你添加你的参数，选择是否加密它们，然后选择谁可以读取这些秘密。我们将允许我们的 Lambda 函数一开始运行就读取这些秘密。由于 Lambda 函数被重用，这只会发生在 Lambda 的第一次调用(第一次 API 调用)。为此，我们添加了/{env}/env_variable 层次结构的参数，例如/production/MYSQL_PASSWORD。现在，我们可以读取所有/production 参数，并将它们用作环境变量，或者只是将它们存储在内存中。

### **#9。性能和冷启动**

当一个 Lambda 在一段时间内没有被调用时，它将被冻结，下一次调用将导致启动一个新的服务器实例的时间。这可能需要一些时间，取决于应用程序的复杂程度，有时在 600 毫秒到 2000 毫秒之间。除了(1)预热 Lambda(使用监控服务定期调用它，或者使用 CloudWatch 进行另一次预定的 Lambda 调用)和(2)让 Node.js 应用程序加载更快，目前还没有真正的解决方案。希望 AWS 将来能找到减少冷启动时间的方法。

如果您的 API 服务器必须符合 SLA，那么此时无服务器可能不太合适😞

### **#10。没有并行请求**

在构建 Node.js 服务器时，我们习惯在事件循环和异步函数的帮助下处理多个请求。然而，当在 AWS Lambda 中运行时，每个 Lambda 容器将只处理一个请求。

这意味着**并行性是通过 API 网关**产生多个 Lambdas 而不是一个 Node.js 应用服务多个请求来实现的。

测试你的应用和用例，看看这个模型是否合适。

## 结论

无服务器是运营领域的一大进步吗？使用 **devops** 时，我们希望了解运营是如何工作的，而使用无服务器时，我们受益于将运营责任委托给其他人(在这种情况下是 AWS)，我们可以称之为**无运营**。虽然我们失去了灵活性，但我们获得了许多功能、轻松的心态和将精力集中在代码和产品上的能力。

无服务器在未来几年肯定会占据主导地位，包括更具体的无服务器产品，如无服务器数据库、无服务器流媒体服务等。

对于我们开发人员来说，这几乎是圣杯。建造它，运输它，它能工作。

最初发布于[https://hacker noon . com/learned-a-year-going-full-server less-in-production-3d 7 e0d 72213 f](https://hackernoon.com/lessons-learned-a-year-of-going-fully-serverless-in-production-3d7e0d72213f)