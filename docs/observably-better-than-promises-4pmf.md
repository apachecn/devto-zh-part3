# 明显快于承诺

> 原文：<https://dev.to/deanius/observably-better-than-promises-4pmf>

在我的上一篇文章中，我解释了承诺可能会减慢你的应用的 5 种方式。前两个问题可以通过改变你对承诺的态度来解决，后三个问题如果你只使用承诺的话，你将会陷入困境。通常情况下，在连接缓慢或断断续续的情况下，或者在设备动力不足的情况下，查看您的应用程序可能会发现它的性能不够好。因此，为了服务尽可能广泛的受众，我认为最佳实践是在你的应用程序中“尽可能少地浪费时间”，Observables 可以帮助你做到这一点。

让我们重新审视一下承诺缓慢的设计原因，看看观察如何解决这些问题。

*   承诺内的同步值仅在以后可用
*   单一值限制否定了流式传输的优势
*   无法取消意味着资源被占用的时间更长

### 同步值，内部的可观测量可以同步使用

回想一下，这个 Promise 示例不会提醒最后一行的新状态。

```
let status = 'not loaded'
Promise.resolve('loaded').then(newStatus => {
  status = newStatus
})
console.log(status) // logs 'not loaded' 
```

Enter fullscreen mode Exit fullscreen mode

然而，这个可观察的版本实际上将记录“已加载”，因为从`of`返回的可观察数据已经:

```
const { of } = rxjs
let status = 'not loaded'

of('loaded').subscribe(newStatus => {
  status = newStatus
})
console.log(status) // logs 'loaded' 
```

Enter fullscreen mode Exit fullscreen mode

这为什么有用？

因为 RxJS(一般来说是可观察的)被设计为“尽可能少地浪费时间”，所以如果要处理的数据是同步可用的，那么通过可观察的来处理它们会产生最小的开销。实际上还有一个更大的主题，称为[调度器](https://blog.strongbrew.io/what-are-schedulers-in-rxjs/)，它解释了如何重新分配可观察对象，以在每个动画帧上运行同步、异步或许多其他丰富的选项。但与承诺相比，要记住的一点是，承诺没有内在的减速。

### 可观测量是多值的，支持增量交付

我提到了来自远程服务器的基于承诺的用户`fetch`在最终的`]`返回并且 JSON 可以被解析之前不会让任何用户对浏览器可用，以及这对于缓慢或间歇的连接是如何的不利，这可能会丢失他们已经接收到的所有数据。

考虑许多项的 REST 结果的可观察性的方法是，它类似于在一个目录上启动一个`ls -l`进程。返回的每一行都可能是您正在寻找的行，所以每一行越早显示给用户，用户就能越早处理它，并且可能通过取消列表来释放资源，如下一节所述。

下面是一些承诺代码，用来获得一个虚构的旅馆，加州旅馆的一堆房间的入住率..数据看起来像:

```
[
  {"room":"30","occupancy":"open"},
  {"room":"31","occupancy":"full"},
...
] 
```

Enter fullscreen mode Exit fullscreen mode

而等待他们的得到承诺的版本看起来都是:

```
 callApi("/api/occupancy").then(records => {
      records.forEach(record =>
        store.dispatch({ type: "setOccupancy", payload: record })
      );
    }); 
```

Enter fullscreen mode Exit fullscreen mode

同时，使用来自`rx-helper`库的`ajaxStreamingGet`的可观察版本看起来像:

```
ajaxStreamingGet({ url: "/api/occupancy" }).subscribe(
  record => store.dispatch({ type: "setOccupancy", payload: record })
); 
```

Enter fullscreen mode Exit fullscreen mode

几乎相同的代码，但可观察版本的数据使用时间要低得多。看看这个通过一个变慢的端点点击 Github API 的现场演示。在其他条件相同的情况下，越早获得的数据越有价值！如果用户看到了他们想要的，或者在结果下载之前进入了另一个页面，因为这是可观察的，而不是承诺，它可以被取消。所以让我们深入研究一下..

### 观察值是为计划和取消而设计的！

我听过的好建议是*“不要开始你不知道如何完成的事情”*。在计算领域，当需求发生变化时，有时必须关闭一些东西，以免造成浪费。或者毁灭整个星球！让我们看一个 10 秒内发起核战争的承诺:

```
const initiateLaunch = new Promise(resolve => {
  console.log("FIRING ZE MISSLES IN 10 SECONDS!"); 
  const timeoutId = setTimeout(() => {
    console.log("LAUNCHED")
    resolve("were all goners")
  }, 10*1000)
})

initiateLaunch.then(result => console.log("Uh Oh: " + result)) 
```

Enter fullscreen mode Exit fullscreen mode

首先要警惕的是，仅仅定义承诺的行为就让导弹开始发射。既然承诺不能取消，事实上，我们都要完蛋了！

可观测量不是这样工作的。它们最好被认为是索引卡上的食谱——仅仅阅读食谱并不会导致烹饪开始。它们就像弦`ls -l`一样，弦*可以被转化为一个运行的过程*，这个过程将产生结果，但还没有开始运行。这就是所谓的“可观察的人是懒惰的”。挺好的。这意味着你可以定义导弹发射过程，而无需启动它。

至于取消，当你定义一个可观察的，你可以(也应该！)提供取消它的方法。你返回一个包装了取消逻辑的函数，可观察对象的用户用方法‘unsubscribe’取消它(你能猜到你是如何启动可观察对象运行的吗？).综上所述，我们有:

```
const initiateLaunchSafe = new Observable(notify => {
  console.log("FIRING ZE MISSLES IN 10 SECONDS!"); 
  const timeoutId = setTimeout(() => {
    console.log("LAUNCHED")
    notify.complete()
  }, 10*1000)

  // Make available the means to cancel by returning a function
  return () => clearTimeout(timeoutId)
}) 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。与 Promise 版本不同，这段代码不会立即运行——要启动它，我们必须首先调用`subscribe`。但是更进一步，让我们想象一种情况，这个发射序列被定义，然后 1 秒钟后，我们取消它。我们能做到吗？？

```
 // Calling subscribe starts it, and returns a Subscription object
const launchSequence = initiateLaunchSafe.subscribe()

setTimeout(() => {
  launchSequence.unsubscribe()
  console.log("SAVED THE PLANET!")
}, 1 * 1000) 
```

Enter fullscreen mode Exit fullscreen mode

事实上，我们可以，也确实通过使用可观测的东西来拯救地球。我做了这个 [JSBin](https://jsbin.com/diyurej/edit?js,console) ，你可以在这里自己玩拯救地球。

### 结论

可观测量是尽可能少浪费时间的优秀通用工具。这一切之所以成为可能，是因为他们支持:

*   可用时同步操作
*   增量结果
*   取消以释放资源

虽然有一些抽象概念使承诺成为可能，但我可以再写几篇文章，介绍 Observables 对于简单、简洁地建模问题的优势，并编写比承诺更多的声明性代码。虽然编程中没有灵丹妙药，但有时正确的抽象层可以让复杂性消失。我们不再操纵内存地址来获得数组的不同元素(就像在 C 语言中需要的那样)，因为数组抽象将我们从低级的东西中拯救出来。类似地，如果我们只使用承诺，可观察性将我们从时间和资源管理问题中解救出来。

在工作中总是使用正确的工具，你的用户、团队和公司会感谢你的。请在下面的评论中告诉我你的想法！

教长