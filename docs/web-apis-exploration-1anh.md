# Web APIs 探索

> 原文：<https://dev.to/areknawo/web-apis-exploration-1anh>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

JavaScript 允许开发者在不同的浏览器上为用户创造惊人的交互体验。事实上，这就是为什么所有的网络内容在过去的几年里变得如此引人入胜——因为 JS！但是仅仅一种编程语言不足以驾驭网络的力量。不，不。你需要的远不止这些！当然，有大量的 JS 内置类型、对象、类等等。甚至在低级代码中提供不同的功能。但这仍然不够。JavaScript 被**沙箱化**在浏览器内部，自身无法访问外部世界。我这里说的是多线程、3D 图形、控制、计算机状态(电池、连接、内存等)等功能。)和计数。这就是为什么**Web API**存在的原因。Web APIs 是一个术语，指浏览器提供的所有 API(应用程序编程接口),用于通过 JS 代码访问设备的不同本机功能。此外，在 Web APIs 中，还有一些个体，如用于访问 DOM (HTML 集成)的 **DOM API** 和用于 CSS 的 **CSS API** 。请注意，这些是 API，因为它们不仅可以从 JavaScript 访问，也可以从其他语言访问，例如以前不受欢迎的 **VBScript** in **IE** 。现在，这个问题已经解决了，您可能在某个时候至少使用过一个 Web APIs。你甚至可能不知道，因为有太多的库为它们提供了更容易使用的接口。其中一个很可能是 **DOM** 、 **CSS** 、 **Audio** ，甚至是 **Canvas/WebGL API** 。这些都是大而复杂的 API，可能需要一些时间来学习。但是相信我，这是值得的！

# 相识

除了上面列出的 API，还有很多。我想向你介绍其中的一些...至少那些小的。值得一提的是，API 自然不是可以用 Babel 或其他工具填充的东西。这就是浏览器支持在这个领域极其重要的原因。有些甚至可能不是官方的，只受特定浏览器的支持。只有那些具有良好的跨浏览器支持和/或被正式标记为 W3C 规范的一部分的软件才包含在这个列表中。当然，它们中的许多都很新，在生产中使用之前，你应该检查一下像**can use**或其他类似的工具。考虑到这一点，让我们来看看一些最被低估或者不太流行/知名的 Web APIs。

# [服务人员](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) & [推送 API](https://developer.mozilla.org/en-US/docs/Web/API/Push_API)

服务人员(SW)T1 和 T2 推送 API T3 都是非常新但需求很高的 API。SW 作为一个**后台进程**允许服务器和 web 应用程序之间的交互，即使当它关闭时。它与 Push API 密切相关，后者允许接收服务器发送(推送)的消息。你看到了一个模式，对吗？服务器发送一些数据，您的 web 应用通过软件和推送 API 与之交互，例如显示通知(借助**通知 API** 提供的一些附加软件方法)。这些 API 主要是为了让 web 应用程序感觉更自然而向前迈进了一大步。无论如何，整个概念可能看起来有点复杂。基本工作流程要求您下载、安装并激活服务人员。至于 Push API，您需要使用例如 **Fetch API** 订阅服务器消息。这将需要一篇单独的文章，所以，现在，我将让您进一步探索这个主题。请记住浏览器支持——您应该熟悉 Safari 推送 API 的自定义实现。

# [加密 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API)

继续复杂 API 的列表，这里是**加密**。这个 API 允许开发者使用**密码**原语，自然地，用于密码目的。令人惊讶的是，它有相当好的浏览器支持，甚至 IE 11 也有部分(前缀)支持。这可能是最神秘的 API 之一。这很可能是由于它的用例。引用 **MDN 网络文档**:

> 如果你不确定你知道你在做什么，你可能不应该使用这个 API。

所以，除非你在做这种加密的东西，否则最好不要管它，只要知道它的存在就好。

# [付款申请 API](https://developer.mozilla.org/en-US/docs/Web/API/Payment_Request_API)

这是事情开始变得多一点的地方...可用。😅**支付请求 API** 致力于提供一种以标准、统一的方式进行网上购物的简单方法，但由于显而易见的原因，只能在 **HTTPS** 进行。可悲的是，这很棒，但是这个新想法*没有足够好的跨浏览器支持*来被认为是可靠的。但是，由于它是一个 W3C 标准，支持将会增加，最终我们都将有一个简单的方法来进行在线支付。现在，让我们来看看这个早期的 API，好吗？

```
if (window.PaymentRequest) {
  const payment = [
    {
      supportedMethods: "basic-card",
      data: {
        supportedNetworks: ["visa", "mastercard"],
        supportedTypes: ["debit", "credit"]
      }
    }
  ];

  const cart = {
    id: "order-1",
    displayItems: [
      {
        label: "Example item",
        amount: { currency: "USD", value: "1.00" }
      }
    ],
    total: {
      label: "Total",
      amount: { currency: "USD", value: "1.00" }
    }
  };
  const request = new PaymentRequest(payment, cart);

  request
    .show()
    .then(function(response) {
      response.complete("success").then(function() {
        // Handle success
      });
    })
    .catch(function(error) {
      // Handle cancellation or failure.
    });
} else {
  // Payment Request API is unsupported
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我能想到的最直接的例子。首先，我们检查 API 是否可用。然后是一些硬编码的数据定义，以备后用。最后是主呼叫。之后的一切都只是展示了显示支付模式和处理不同结果是多么容易。剩下的只有等这个得到更好的跨浏览器支持了。手指交叉。😁

# [性能 API](https://developer.mozilla.org/en-US/docs/Web/API/Performance_API)

顾名思义，**性能 API** 或者更确切地说是**用户计时 API** 可以用来测量**你的 JavaScript 代码的性能**。信不信由你，它做得相当好。精确度高达千分之一毫秒，它可能是这项工作的最佳工具之一。浏览器支持不出所料的好，连 IE 10 都支持。现在，这个 API 为您提供了许多用于基准测试的小方法。您可以检索计时、设置时间标记并观察不同的性能相关事件。为了方便起见，我们来看一个基本的例子。首先，让我们定义一个将要进行基准测试的函数。

```
function benchmarkMe(){
    for(let i = 0; i < 1000; i++){
        console.log(i)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，包含 1000 个 console.log()的标准循环可能要求很高。

```
const timeBefore = performance.now();
benchmarkMe()
const timeAfter = performance.now();
console.log(`BenchmarkMe() took ${timeAfter - timeBefore} ms.`); 
```

Enter fullscreen mode Exit fullscreen mode

这个你可能很熟悉。假设这是在 JS 中测量性能的标准方法。现在，让我们尝试一种更奇特的方式...

```
performance.mark("timeBefore")
benchmarkMe()
performance.mark("timeAfter")
performance.measure("time", "timeBefore", "timeAfter")
console.log(`BenchmarkMe() took ${performance.getEntriesByName("time")[0].duration} ms.`); 
```

Enter fullscreen mode Exit fullscreen mode

在这个场景中，我们使用时间标记来做我们以前做过的事情。当你在做一些更大的基准测试时，这种技术更有用，对于一个简单的函数基准测试来说，这可能太多了。但是，你知道最基本的。😄

# [振动 API](https://developer.mozilla.org/en-US/docs/Web/API/Vibration_API)

这可能是最简单的 Web APIs 之一。当然，它可以用来与负责**振动**特定设备的硬件(如果存在)进行交互。这一切都归结于一个方法。

```
// Vibration time in miliseconds: 200ms
window.navigator.vibrate(200);
// Vibration sequence: 200ms vibrate, 100ms stop, 200ms vibrate
window.navigator.vibrate([200, 100, 200]);
// Vibrate for a long time...
window.navigator.vibrate(999999);
// ...but stop after 1 second
setTimeout(() => {
    window.navigator.vibrate(0);
}, 1000) 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。只是要记住，Safari 不会允许你振动这么多，或者说根本不允许。

# [剪贴板 API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API)

也许这个 API 还很年轻，没有很好的跨浏览器支持，但是它提供了一些有趣的功能。顾名思义，它只允许你通过 **HTTPS** 与用户的剪贴板进行异步交互(剪切、复制、粘贴)。它主要归结为 4 种方法:`read()`、`readText()`、`write()`、`writeText()`，它们都返回一个带有可选剪贴板数据的承诺，用于读取方法。出于安全原因，该 API 要求您使用**权限 API** 授予显式权限。

# [页面可视性 API](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API)

这个问题归结为两个属性和一个事件。它允许检查一个用户是否已经打开了你的页面**(例如没有最小化)，以及页面是否已经**呈现**或者正在**卸载**的过程中。这些属性是`document.hidden` ( **布尔**)和`document.visibilityState` ( **字符串**)。您还可以通过监听`onvisibilitychange`事件来了解值的变化。**

 **# [全屏 API](https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen_API)

**全屏 API** 向文档和元素接口添加方法，允许它们转入**全屏模式**。这两个是`requestFullscreen()`和`exitFullscreen()`，都是返回一个承诺。该 API 还提供了一些事件和属性来检查特定元素是否处于全屏模式等等。没什么特别的，但对画布、视频和图像元素非常有用。

# 越多，越数越多...

这些只是一小部分，是 Web APIs 所能提供的一瞥——现在可用的和将来计划的。有些只不过是一些有用的方法和属性，有些非常非常复杂。我鼓励你看一看并发现其他的 Web APIs，以便成为一名更好的 Web 开发人员，并理解不同的库如何将这些 API 封装到好的包中。出于这个目的，我推荐你去看看像[**【MDN】**](https://developer.mozilla.org/)(链接在每个列出的 API 中)**can use**(用于浏览器支持)和 [**WhatWebCanDo**](https://whatwebcando.today/) (发现一些有趣的 API 和它们的功能)。此外，如果你喜欢这篇文章，可以考虑使用下方的*圆形按钮和*在 **Twitter** 或我的**脸书页面**上关注我的*来分享更多最新内容。🎉***