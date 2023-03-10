# 承诺如何减缓你的应用程序

> 原文：<https://dev.to/deanius/5-ways-promises-may-be-slowing-your-app-hgm>

你好 Dev.to 社区，这是我的第一篇帖子！(编辑:这里还有第二个的[，详细阐述了这个)](https://dev.to/deanius/observably-better-than-promises-4pmf) 

```
usedToLovePromises.then(() => console.log( 😡😤 )) 
```

Enter fullscreen mode Exit fullscreen mode

作为回拨地狱的解决方案，承诺一度成为我的新宠。我就此发表演讲——甚至在玩杂耍的时候，因为——并发性🤣 [Youtube](https://www.youtube.com/watch?v=dyz3tAI6GaI&t=37) 。

但是我意识到，就像每一种技术一样，它们也带来了一些问题。所以，在我告诉你我现在在用什么之前(RxJS Observables with [复节奏](https://github.com/deanius/polyrhythm))，让我们来看看承诺可能会减缓你今天的应用的 5 大方式:

*   循环中的 Await 会破坏并行性。
*   等待将被拒绝的承诺变成例外。
*   承诺内的同步值仅在以后可用。
*   单一值限制否定了流式传输的优势
*   无法取消意味着资源的消耗时间可能会超过消费者的需求

前两个你可以解决，剩下的需要更大的改变，但是仍然可行....让我们开始吧！

### 循环中的 Await 破坏并行性

```
async function(usernames) {
  let users = []

  for(let name of usernames) {
     users.push(await fetch(`http://server/users/${user}`))
  }

  return users;

}(['bob', 'angie']) 
```

Enter fullscreen mode Exit fullscreen mode

这个代码有什么问题？它旨在将一组用户名转换成由某个远程服务返回的完整对象。这是一种兑现承诺的常见做法。但是当您一次等待一个用户时会发生什么呢？没错—您不再能够并行检索用户。假设你的回答用了 1 秒和 1.5 秒——不是让所有用户在 1.5 秒内回复，而是让他们在 2.5 秒内回复。总持续时间不再是`max(times)`，而是需要`sum(times)`。

解决方法:使用 [Promise.all](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)

### 使用 Await 将被拒绝的承诺变成程序崩溃异常

虽然在快乐的道路上是美好的，但不快乐的道路可能是丑陋不堪的。

根据您的承诺-返回 AJAX 库，有时从服务器收到的 500 错误代码会导致返回的承诺被拒绝。如果你在一个被拒绝的承诺上使用`await`,你会得到一个同步异常！

同步异常“撕裂调用堆栈”，这是“花很长时间做它们所做的事情”的一种奇特说法。更令人担忧的是，它们可能会使您的整个应用程序瘫痪！我们不希望我们的应用程序变得脆弱。有时无状态服务可以重启，但这也需要时间。

当 HTTP 200 和 HTTP 500 响应之间可能只有一个字节的差异，当它们都表示内容从服务器成功传输到您的应用程序，并且完全可以预见服务器有时会很慢或不可用时，`await`开始看起来像一个真正的 foot-gun。

解决方法:仅当您有顶级异常处理时，才在应用程序中使用`await`。对于有合理的失败机会的操作，不要使用`await`,因为终止一个正在运行的应用程序不是一个合适的响应。

### 按设计:同步值，承诺内只有以后才可用

```
let status = 'not loaded'
Promise.resolve('loaded').then(newStatus => {
  status = newStatus
})
console.log(status) 
```

Enter fullscreen mode Exit fullscreen mode

上面的`status`值是多少？答应粉丝知道会是`"not loaded"`。这很有道理，因为——异步。就像[杰克·阿奇博尔德提到的](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)，在当前时刻之后，决心尽可能快地发生。但是为什么要推迟呢？

这个问题的答案是，根据设计，即使承诺现在*被解析*，它的回调在稍后被调用*，以解决模糊性。这有点像牙医先发制人地拔掉你所有的牙齿，但嘿，至少它不会释放 Zalgo！*

挑战:集成异步值的承诺用法，以及同步可用值的不同技术。

### 按设计:单值限制否定了流优势

如果您的服务器流式传输一个大的 JSON 文档来响应`/users`，在每个用户之后刷新它的响应流，并且您的客户端应用程序获得了前 50 个用户，但是对结果用户数组使用了一个承诺，那么您的用户将被阻止看到前 50 个用户，直到所有用户都被返回！而如果服务器未能返回 JSON 中的 final `]`，用户的浏览器(或者你的节点进程)会把所有数据都存在内存中，但是无法使用！(问我怎么知道的！)

这可能看起来有点迂腐，但是就像 Juan Caicedo 提到的，所有用户并不总是处于快速或可靠的连接中。

为什么这里要指责承诺？因为在浏览器拥有完整的用户群之前，它无法兑现承诺。

我有一个关于如何用一个可观察的和一个承诺来查询同一个端点的小演示，这是没有争议的——可观察的，因为它代表了一个用户流，能够一次交付一个用户，而承诺变体在完成之前不会向 UI 交付任何东西。比起单值承诺，业绩只是更喜欢多值可观测量的一个原因，但它是一个有效的原因。

挑战:处理多个承诺并把它们联系起来是很困难的。

### 不能取消意味着资源被占用的时间更长

对于任何在内存/网络受限设备上工作的人来说，这可能是最大的障碍。这就是为什么布莱恩·霍尔特做了一个演讲[承诺不使用承诺](https://www.youtube.com/watch?v=DaCc8lckuw8)，虽然很老，却远远领先于它的时代。如果您在一个代表聊天室的路由上启动一个 AJAX 请求，而用户导航到另一个房间，Promises 不会让您取消第一个请求！所以在最好的情况下，新房间消息的可用带宽减半，在最坏的情况下，浏览器已经用完了它的[最后可用连接](http://sgdev-blog.blogspot.com/2014/01/maximum-concurrent-connection-to-same.html)，新房间的消息被阻塞！

这不是一个快乐的地方。当应用程序知道它不需要某个进程的结果时，它需要能够立即关闭该进程并释放其资源。正如我的一个朋友说的“不要开始你不知道如何结束的事情”。适用于计算机和儿童游戏。:)

挑战:不取消是浪费，但是手动使用取消令牌是很棘手的。*(这就是为什么[TC-39 增加承诺可取消性的提议](https://github.com/tc39/proposal-cancelable-promises)失败了！)*

## 结论

我将很快写下我认为 Observable 数据类型如何解决上述问题，(以及它带来的新挑战)，但那是以后的事了。现在，只要知道这些是需要注意的可能的承诺陷阱。与回调相比，它们仍然是一个巨大的进步，并且是一个 goto 工具，直到你意识到[可观测量是承诺的超集](https://dev.to/deanius/observably-better-than-promises-4pmf)

[![Observe](img/6a10ac22be2a4c77144164003f9fc0fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EiqmNZi7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9b5wrh6hftwextbtlk4u.JPG)