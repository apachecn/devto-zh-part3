# 您是否应该记录 Express req 对象和外部 API 响应？

> 原文：<https://dev.to/ccleary00/should-you-log-the-express-req-object-and-external-api-responses-pj2>

***原载于 [coreycleary.me](https://www.coreycleary.me/should-you-log-the-express-req-object-and-external-api-responses/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我也定期发送备忘单和其他赠品。*

记录尽可能多的信息，以便能够进行故障排除、了解会话期间发生的事情，甚至出于分析目的，这是所有应用程序在投入生产之前都需要具备的。

您可能已经*记录了一些*——比如错误和成功的事务(如果您正在查看分析/度量的日志)。但是现在你想知道你*还应该做什么*记录，这样你在生产中遇到问题时就有了你需要的一切。

您可能认为 Express `req`对象(request object)包含了发送给 API 的所有信息，应该记录下来。“太好了，这将为我提供有关会话的所有信息！”

类似地，如果您正在调用一个外部服务，而该服务不是您所拥有的，并且对日志记录没有控制权(例如，Twitter API，或者甚至是您公司拥有的 API，但是该 API 从未更新过，也没有日志记录)，那么从 axios/superagent/您正在使用的任何东西记录响应对象可能是有意义的！

但是这些事情真的应该被记录吗？获得更清晰的答案将有助于您获得坚如磐石的日志——您可以轻松地使用日志进行故障排除，而不必在检测到生产中的问题时浪费时间。

### 直接的不利因素

首先，整个`req`对象带有一系列**信息——主体、头、参数、cookies、查询、应用程序、客户端、url 等等。**

类似地，来自外部 API 调用的响应可能会包含一堆您不需要的信息。

这是太多的信息，大部分都没有用，很难通读日志(您可能需要 JSON.stringify()它，由于循环引用，它甚至可能不起作用)，并且它会占用更多的日志空间。

### 有些东西呢，比如主体/头/查询/参数？

从表面上看，就像`req.body`说的那样，伐木更有意义。这是一组更有限的数据，我们避免了上面讨论的缺点。

我们可以更容易地搜索日志，噪音更少，使用请求信息进行故障诊断将非常方便。

但是有一个问题可以不被注意到: **[个人身份信息](https://searchfinancialsecurity.techtarget.com/definition/personally-identifiable-information)** (PII)。

### 血浆无机碘

一般来说，这些数据是保密的，可以消除用户的匿名性，只能由贵公司的某些成员访问，或者根本不能访问。根据您正在构建的应用程序的类型和您有什么样的法规遵从性需求(无论是由像 [HIPPA](https://searchhealthit.techtarget.com/definition/HIPAA) 这样的东西定义的，还是仅仅是由安全专业人员在您公司内部设置的规则)，什么使得 PII 会有所不同。

但一般来说，PII 通常是社会安全号码，驾照号码，银行账户信息，诸如此类的东西。

假设您有一个购买保险的 web 表单，该表单记录了用户的姓和名、驾照号码、州名和其他有关用户的数据。它会被发送到您的 Express API 以及您可能拥有的任何其他微服务。

如果驾照号码不小心被记录了，那就麻烦了。

因此，这是另一个原因，不仅您通常不应该记录整个请求和/或响应，而且还应该小心记录这些对象的哪些属性。否则，PII 的数据可能会溜进来。

### 一个解决方案

继续以保险表单为例，假设我们想要记录请求中的其他信息。您可以通过析构来获得日志记录所需的非 PII 数据:

```
// pseudocode-ish
const {state, purchasedPlan} = req.body

console.log({state, purchasePlan}) 
```

或者您可以使用一些通用的实用函数来检查 req.body 的每个属性。

方法一:

```
// first approach, remove PII properties from the request
const safeLog = (data) => {
  const piiProps = ['ssn', 'driverLicense']
  const safeData = {}

  // assumes data is an object (like req.body)
  for (const prop in data) {
    const value = data[prop]
    if (!piiProps.includes(prop)) {
      safeData[prop] = value
    }
  }  

  const hasDataToLog = Object.entries(safeData).length > 0 

  if (hasDataToLog) console.log(safeData) 
}

// req.body is: {ssn: '123-45-6789', purchasedPlan: 'Silver'}
safeLog(req.body) // only logs {purchasedPlan: 'Silver'} 
```

这种方法的缺点是，您可能会拼错想要取消的 PII 属性，或者请求本身的属性可能会拼错。也就是说，req.body 可以是:`{sn: '123-45-6789'}`，在这种情况下，它不会被`safeLog()`函数捕获。

方法二:

```
// second approach, check by regex
const safeLog = (data) => {
  const socialSecurityRegex = new RegExp(/^\d{3}-\d{2}-\d{4}$/) // assumes is in 123-45-6789 format
  const safeData = {}

  // assumes data is an object (like req.body)
  for (const prop in data) {
    const value = data[prop]
    if (!socialSecurityRegex.test(value)) {
      safeData[prop] = value
    }
  }  

  const hasDataToLog = Object.entries(safeData).length > 0 

  if (hasDataToLog) console.log(safeData) 
}

// req.body is: {ssn: '123-45-6789', purchasedPlan: 'Silver'}
safeLog(req.body) // only logs {purchasedPlan: 'Silver'} 
```

这使我们避开了前一种方法的问题，但缺点是我们可能有其他数据不是 PII 的，而与 PII 的一些数据的正则表达式相匹配。

### 包扎

我倾向于采取析构我需要记录的东西的方法。这意味着您和您的团队必须仔细考虑您选择的日志，以确保它不会 A)占用不必要的日志空间/在故障排除时难以阅读，以及 B)不违反 PII 数据规则。

但是这是无论如何都应该在代码审查/拉取请求中捕捉到的东西。

感觉你还没有完全掌握你应该登录什么节点？或者想知道您是否遵循了最佳实践？我每一两周发布一次关于 JavaScript 和 Node 的新帖子，包括日志、测试和架构。再次点击链接订阅我的时事通讯！