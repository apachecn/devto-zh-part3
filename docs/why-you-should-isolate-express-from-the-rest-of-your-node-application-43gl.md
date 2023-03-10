# 为什么应该将 Express 与节点应用程序的其余部分隔离开来

> 原文：<https://dev.to/ccleary00/why-you-should-isolate-express-from-the-rest-of-your-node-application-43gl>

***原载于 [coreycleary.me](https://www.coreycleary.me/why-you-should-isolate-express-from-the-rest-of-your-node-application/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我还定期发送备忘单、其他开发人员的优秀教程的链接以及其他赠品！*

你可能听说过你应该“总是将你的应用分层”和“永远不要让逻辑泄露到其他层”。

这些都是在你最喜欢的博客上，在“必读”编程书籍中，以及在技术聚会和会议上发表的言论。

但是你可能想知道*为什么*这确实是个问题！所谓的“最佳实践”经常被这样提出，但是没有解释**如果你不遵循它们会发生什么**以及**它们最初是如何成为最佳实践的。**

因此，让我们来看一看**一个**问题，如果你不分层构建你的应用程序，你会在使用 Express REST API 时遇到这个问题。然后，我们将寻找一个简单的解决方案来避免将来出现这种问题(如果有问题，就修复它)。

### 眼睛所能看到的物体

你有没有过这样的代码？
[![](img/8ec17de7fb479b97bba66520ae7debef.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZOepI2_S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.coreycleary.me/wp-content/uploads/2018/11/bad-way.png)

express `req`对象不仅在您的控制器中，也在您的服务层中！并且可能——甚至更糟——`req`对象从服务层传递到数据库层！

这违反了“永远把你的 app 分层，永远不要让逻辑漏到其他层”。

现在，原因**为什么**这是一个问题:

### 要表达的婚姻

首先，现在应用程序的其余部分不仅依赖于`req`对象，还依赖于 Express 框架。如果将来你想转到哈比神或者 Koa 会怎么样？然后，您必须找到所有对`req`的引用，并删除/替换它们。然后你必须确保测试仍然有效！

对于小型应用程序，这可能不是很多要解决的工作，但如果它是一个有很多路线/控制器和很多人在工作的大型应用程序，改变它会变得非常痛苦。

如果您错过了某个参考文献，而您的测试没有发现它，该怎么办？你的用户肯定会的...

### 使得测试更加麻烦

这也增加了测试的难度。现在`req`被传递给我们的服务函数，在我们的测试中，我们必须模拟/再现那个对象。

如果您只关心对象的一两个属性，这就相当容易了。但是如果你有很多东西呢？如果你需要检查一个`req`头怎么办？那么人工复制就变得很困难。

然而，如果`req`仅限于您的 HTTP 层，您可以使用 supertest 或类似的东西通过集成测试来测试您的控制器，并且您根本不需要模仿`req`!并且你将有更多的纯单元测试来测试你的服务层功能。

### 混合概念

最后，我们将应用程序分层的很大一部分原因是因为它减少了我们必须处理的“精神负担”。

构建软件是困难的，必须在我们的大脑中处理多种事情变得具有挑战性。当然，如果你已经越过 HTTP 层进入服务/业务逻辑层，它只是一个对象，从表面上看，似乎不难理解。**但是**你已经有了自己的商业逻辑来推理，为什么还要添加其他东西呢？现在，HTTP 的东西与您的业务逻辑混合在一起，这难道不令人困惑吗？

### 避免/修复

现在我们已经讨论了**为什么**这是一个问题，让我们讨论如何避免/解决它。

在你的`controller` :

```
const { blogService } = require('../services')
const { createBlogpost } = blogService

const postBlogpost = async (req, res, next) => {
  const {user, content} = req.body
  try {
    await createBlogpost(user, content)
    res.sendStatus(201)
    next()
  } catch(e) {
    console.log(e.message)
    res.sendStatus(500) && next(error)
  }
} 
```

这里的关键行是:`const {user, content} = req.body`

我们正在做的是**析构**的`req`对象——在本例中是`body`属性——只取出`createBlogpost`服务关心的数据。所以现在，Express 仅限于我们的 HTTP 层，我们没有将请求/HTTP 逻辑泄露到服务层。

如果我们想在未来替换掉 web 框架，这样做要快得多，也简单得多。`req`对象仅限于我们的控制器！现在，我们可以测试我们的其他层，而不用模仿请求对象。

### 包扎

所以要记住——Express 是**入口点**，而不是整个 app。它的“上下文”应该限于 HTTP 层，而不应该泄露到业务逻辑/服务层。

如果你想了解更多关于 REST API 分层的知识，请查看我发现的构建/分层我的应用程序的最佳方式。

如果你觉得这篇文章有帮助，[这是再次链接](https://www.coreycleary.me/about/)注册我的时事通讯，以便在它发布后立即接收新内容。谈到 Node(以及一般的 JavaScript ),有很多东西需要学习——如何编写测试，如何构建应用程序，如何处理异步——我正在编写新的内容来帮助简化它。它不需要像有时那样困难！