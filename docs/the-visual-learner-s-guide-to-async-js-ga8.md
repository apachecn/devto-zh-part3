# 异步 JS 的可视化学习指南

> 原文：<https://dev.to/bnevilleoneill/the-visual-learner-s-guide-to-async-js-ga8>

[![](img/c2b9371ba1c6fdb32677a29c648dc756.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fNsM5CEe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AL02MSn60qttc3gXQCwk_ig.jpeg)

你是否曾经观看或阅读了几个小时的教程，但仍然感到困惑？这就是我第一次学习异步 JavaScript 时的感受。我很难清楚地看出 promises 和 async/await 之间的区别，特别是因为在引擎盖下，它们是相同的。

多年来，Async JS 已经发展了很多。教程是很棒的，但是它们经常给你一个快照，告诉你在那个特定的时间点什么是“正确的”做事方法。没有意识到我应该注意内容的日期(😅)，我发现自己把不同的语法混在了一起。甚至当我试图只消费最近的内容时，仍然缺少一些东西。

我意识到外面的很多材料都不符合我的学习风格。我是一个视觉学习者，所以为了理解所有不同的异步方法，我需要以一种符合我视觉风格的方式来组织它们。在这里，我将通过例子和类比向您介绍我提出的关于异步的问题，以及我如何区分承诺和异步/等待。

### 我们为什么需要异步？

从本质上讲，JavaScript 是一种同步、阻塞、单线程的语言。如果这些话对你来说没什么意义，这个视觉帮助我更好地理解异步 JS 如何更省时:

[![](img/14221f80204d045d8b80c7b466e8e16a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pwqOrPPK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADyHHjRYX8soqgtO_b3y15Q.png) 

<figcaption>[粗线=程序正常运行花费的时间。细线=等待网络花费的时间](https://eloquentjavascript.net/11_async.html)。</figcaption>

我们希望对可以在后台发生的事情使用异步方法。当您从数据库中查询一些东西或发出 API 请求时，您不希望整个应用程序等待。在现实生活中，这相当于在洗衣机修好之前，你什么都不能做——不能打电话，不能吃饭，不能上厕所。这不太理想。

开箱即用，JS 是同步的，但是我们有办法让它的行为*异步。*

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 异步进化

在网上搜索“async JS”时，我遇到了许多不同的实现:回调、承诺和 async/await。对我来说，清楚每种方法及其独特的价值主张很重要，这样我就可以始终使用一致的语法进行编码。以下是每一项的细目分类:

#### **回调**

在 ES6 之前，我们使用回调来实现这种异步行为。在这里我不会深入探讨，但简而言之，回调是一个函数，你把它作为一个参数发送给另一个函数，一旦当前函数执行完毕，这个函数就会被执行。这么说吧，人们称之为“回调地狱”是有原因的。

为了控制事件的顺序，使用回调，您必须将函数嵌套在其他函数的回调中，以确保它们按照您期望的顺序发生。

[![](img/53b8f92b7e76b1d6d5c1ae8968f53b7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0qwBc88r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdQgkS279Z-xzfK0rkBBP2A.png) 

<figcaption>【回调地狱】</figcaption>

由于实现这个让我们都很头疼，JS 社区提出了 promise 对象。

#### **承诺**

作为人类，我们更容易理解和阅读同步代码，所以承诺被创造出来，让*看起来*更同步，但*的行为*不同步。理想情况下，它应该是这样的:

[![](img/44d872e500a44bd5e31193cda8dbcb11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---T2OjbQb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvHdVwZOQCGyX-RIK4NJwqQ.png)

这可能看起来不错，但它缺少一些关键元素，其中之一是错误处理。你有没有遇到过无法处理的错误或警告？这是因为发生了一些错误，导致承诺被拒绝而不是被解决。

在上面的代码片段中，我们只处理“成功”的情况，这意味着一个未处理的承诺永远不会被解决，它占用的内存永远不会被释放。如果你不小心，一个承诺会悄悄地失败，除非用 catch 手动处理:

[![](img/43054f03fac6571c77c6acb433b51b76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cFl8HCMS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuvHdR3kMsJtdfPamlgkS7Q.png)

#### **异步/等待**

这是承诺之上的语法糖，有助于代码看起来更具可读性。当我们在函数前面添加 async 关键字时，它改变了它的性质。

异步函数将返回承诺中的值。为了获得这一价值，我们需要。然后()该方法或等待它。

抛开风格和约定不谈，在代码中一起使用不同的异步方法在技术上是可行的，因为它们都实现异步行为。但是一旦你完全理解了它们之间的区别，你就能毫不犹豫地用一致的语法来写了。

由于 async/await 利用了承诺，我最初很难从语法和约定的角度来区分这两种方法。为了弄清楚它们之间的区别，我为每个用例绘制了每个方法及其语法。

### **比较许诺和异步/等待**

这些比较是我最初为自己设计的视觉升级版。**承诺在左边，异步/等待在右边**。

#### **吞噬**

[![](img/11b2569fbce629bc7819086ce99d9b41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l78PnavZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AC6B05ty6ltEtKgS_FEp0Aw.png)

getJSON()是一个返回承诺的函数。对于承诺，为了解决承诺，我们需要。然后()或。抓住它。另一个解决承诺的方法是等待。

注意，await 只能在异步函数内部调用。这里省略了 async 函数，以便更直接地比较这两种方法。

#### **创造**

[![](img/c2cf6ef6985fa76e2e23408fd40a2188.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gBMX0exH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APbuSmkWo3D5jSr39bbiBgQ.png)

这两者都将返回 Promise {: "hi"}。有了 async，即使你没有显式地返回一个承诺，它也会确保你的代码通过一个承诺传递。

resolve()是承诺的执行函数之一。调用时，它返回用值解析的 promise 对象。为了直接比较这种行为，异步方法被包装在一个立即调用的函数中。

#### **错误处理**

[![](img/07b0a50f6bcd7a0f2f7477f0375d74e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kON7frwO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ai1yzaTVK1YO99kzvUn4vjQ.png)

有几种方法可以捕捉错误。一种是使用 then/catch，另一种是使用 try/catch。这两种方式都可以与 promises 和 async/await 互换使用，但这两种方式似乎分别是最常用的约定。

使用 async/await 的主要优势在于错误堆栈跟踪。有了承诺，一旦 B 解析，我们在堆栈跟踪中不再有 A 的上下文。所以，如果 B 或 C 抛出一个异常，我们不再知道 A 的上下文。

然而，对于 async/await，A 在等待 B 解析时被挂起。因此，如果 B 或 C 抛出异常，我们在堆栈跟踪中知道错误来自 a。

#### **迭代**

[![](img/70d37504f6942f4d8dc19d9a3ed946f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fH9vQ_HH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AR5Im4AOl01u-0UY78ieSXw.png)

我在这里使用单个字母作为名称，以帮助您更清楚地看到语法之间的差异。以前，我会通读代码样本，在那里我觉得我必须剔除函数名中的杂草才能理解发生了什么。这让我很分心，尤其是作为一个视觉学习者。

注意，即使每个任务都是异步的，它们也不会同时运行。我将在下面的**并行执行**中谈到这一点。

#### **检测**

[![](img/77e7fae00ad6f42eb8caf5f0cf5661f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D69tmDa4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5KunRgOF8I6aWf9hpjWGcQ.png)

这里有微妙但重要的区别。记住异步函数返回承诺，同样，如果你使用常规承诺，你必须返回它们。

其他需要注意的事项:

*   不将 await 放在 async 前面会导致无法解析的承诺，这将使您的测试结果返回一个误报
*   如果您想要存根一个返回承诺的异步方法，您可以这样做:

[![](img/5605320c8456ff663c61457bcba6eafd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tyjtOUhO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Az0GJsfL3ltDmiQXakiT1OA.png)

既然我们已经讨论了大部分基本场景，那么让我们接触一些关于异步的更高级的主题。

### 并行与顺序异步

由于 async/await 使得语法可读性很强，所以区分并行执行和顺序执行可能会令人困惑。以下是不同之处:

#### **并行执行**

假设你有一个很长的待办事项清单:取邮件、洗衣服、回复邮件。因为这些事情都不相互依赖，所以您可以使用 Promise.all()来运行这些任务。Promise.all()接受一个承诺数组(对于任何 iterable ),当所有异步方法都解析时解析，或者当其中一个方法拒绝时拒绝。

[![](img/e2a5d4f929bf0f4403c59687b01d5cfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jRmFP9uF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AaAAOgSzoSOWqlMAp8GaeoA.png)

#### **顺序执行**

或者，如果您有相互依赖的任务，您可以按顺序执行它们。例如，假设你在洗衣服。你必须按顺序做事:清洗、晾干、折叠。你不能同时做这三件事。因为它是有顺序的，你应该这样做:

[![](img/a87c5a858dee76c6f149c0179f7c7c29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--omc6mRRG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzX1r9QoYPQ-zdz9Idqro5A.png)

这些函数按顺序执行，因为这里的返回值被用作下一个函数的输入。因此，该函数必须等到返回值后才能继续执行。

### 提示成功

每个人都有不同的学习风格。无论我看了多少教程或读了多少博客，我的异步知识仍然存在漏洞。只有当我坐下来，把所有的事情都计划好了，我才最终把所有的事情拼凑起来。

当你遇到一个让你纠结的概念时，不要沮丧或气馁。这只是因为信息没有以符合你学习风格的方式呈现给你。如果你找不到这些材料，那就自己创造并分享吧！你可能会惊讶有多少人和你有同样的感受。

感谢阅读🙌！很想听听你的想法，欢迎发表评论。

在 [Instagram](https://www.instagram.com/edenadler) 上与我联系，并查看我的[网站](https://edenadler.com)👈。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[异步 JS 视觉学习指南](https://blog.logrocket.com/the-visual-learners-guide-to-async-js-62a0a03d1d57)首先出现在[日志博客](https://blog.logrocket.com)上。