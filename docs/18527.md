# Node.js 中异步钩子 API 的实用概述

> 原文：<https://dev.to/puzpuzpuz/a-pragmatic-overview-of-async-hooks-api-in-nodejs-2iol>

<figure>[![](img/bdee9b15f7f38a85f4059f0861c3a2a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_-RQVe8K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AWSM6gbwDbTgzJL-8) 

<figcaption>照片由 [Tom Quandt](https://unsplash.com/@knipsiknips?utm_source=medium&utm_medium=referral) 上传 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

最近我写了一篇关于 Node.js 应用中请求 id 追踪的[帖子](https://medium.com/@apechkurov/request-id-tracing-in-node-js-applications-c517c7dab62d)。提议的解决方案是围绕 [cls 挂钩的](https://github.com/Jeff-Lewis/cls-hooked)库构建的，该库反过来使用 node 的内置异步钩子 API。所以，我决定更加熟悉异步钩子。在这篇文章中，我将分享我的发现，并描述这个 API 的一些真实用例。

让我们以一个简短的介绍开始我们的旅程。

### 异步钩子 API 简介

[Async Hooks](https://nodejs.org/api/async_hooks.html) 是从 [v8.0.0](https://nodejs.org/en/blog/release/v8.0.0/) 开始 Node.js 中可用的一个实验性 API。因此，尽管是一个实验性的 API，它已经存在了大约一年半的时间，看起来没有严重的性能问题和其他错误。实验状态意味着 API 在将来可能会有不向后兼容的变化，甚至可能被完全移除。但是，考虑到这个 API 有几个不那么幸运的前辈，[process . addasync listener](https://github.com/nodejs/node-v0.x-archive/pull/6011)(<v 0.12)和[Async wrap](https://github.com/nodejs/node-eps/pull/18)(V6–7，非官方)，Async Hooks API 不是第一次尝试，最终应该会成为一个稳定的 API。

async_hooks 模块的文档以下列方式描述了该模块的用途:

> async_hooks 模块提供了一个 API 来注册跟踪 Node.js 应用程序内部创建的异步资源的生存期的回调。
> 
> …
> 
> 异步资源表示具有关联回调的对象。这个回调可以被多次调用，例如，在 net.createServer()中的“connection”事件，或者像在 fs.open()中一样只被调用一次。还可以在调用回调之前关闭资源。异步并不明确区分这些不同的情况，而是将它们表示为资源的抽象概念。

因此，异步钩子允许你跟踪任何(嗯，几乎任何)发生在你的节点应用中的异步东西。与代码中任何回调和本机承诺的注册和调用相关的事件可能会通过异步钩子监听。换句话说，这个 API 允许您将侦听器附加到宏任务和微任务生命周期事件。此外，该 API 允许从节点的内置本机模块(如 fs 和 net)监听[低级异步资源](https://nodejs.org/api/async_hooks.html#async_hooks_type)。

核心异步钩子 API 可以用下面的代码片段来表达(这个代码片段的简短版本[，来自文档):](https://nodejs.org/api/async_hooks.html#async_hooks_overview) 

```
const async_hooks = require('async_hooks')

// ID of the current execution context
const eid = async_hooks.executionAsyncId()
// ID of the handle responsible for triggering the callback of the
// current execution scope to call
const tid = async_hooks.triggerAsyncId()

const asyncHook = async_hooks.createHook({
  // called during object construction
  init: function (asyncId, type, triggerAsyncId, resource) { },
  // called just before resource's callback is called
  before: function (asyncId) { },
  // called just after resource's callback has finished
  after: function (asyncId) { },
  // called when an AsyncWrap instance is destroyed
  destroy: function (asyncId) { },
  // called only for promise resources, when the `resolve`
  // function passed to the `Promise` constructor is invoked
  promiseResolve: function (asyncId) { }
})

// starts listening for async events
asyncHook.enable()
// stops listening for new async events
asyncHook.disable() 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到 Async Hooks API 中没有那么多函数，一般来说，它看起来很简单。

函数的作用是:返回当前执行上下文的标识符。triggerAsyncId()函数返回负责调用当前正在执行的回调的资源的 Id(让我们称之为父 id 或触发器 id)。async hook 的事件侦听器中也有相同的 id(请参见 createHook()函数)。

您可以使用 executionAsyncId()和 triggerAsyncId()函数，而无需创建和启用异步挂钩。但是，在这种情况下，由于 V8 中 promise 自省 API 相对昂贵的特性，promise 执行没有分配给[异步 id。](https://nodejs.org/api/async_hooks.html#async_hooks_promise_execution_tracking)

现在，我们将把重点放在异步钩子的行为上，因为如何以及何时触发已创建钩子中的回调并不明显。下一步，我们将使用异步钩子做一些实验，并了解它们是如何工作的。

### 来玩吧！

在做任何实验之前，我们将实现一个非常原始的异步钩子。它将在 init 调用时为事件存储必要的元数据，并为所有后续调用将其输出到控制台。为了最小化控制台输出，它还支持按事件类型过滤。就是这里:

```
const asyncHooks = require('async_hooks')

module.exports = (types) => {
  // will contain metadata for all tracked events
  this._tracked = {}

  const asyncHook = asyncHooks.createHook({
    init: (asyncId, type, triggerAsyncId, resource) => {
      if (!types || types.includes(type)) {
        const meta = {
          asyncId,
          type,
          pAsyncId: triggerAsyncId,
          res: resource
        }
        this._tracked[asyncId] = meta
        printMeta('init', meta)
      }
    },
    before: (asyncId) => {
      const meta = this._tracked[asyncId]
      if (meta) printMeta('before', meta)
    },
    after: (asyncId) => {
      const meta = this._tracked[asyncId]
      if (meta) printMeta('after', meta)
    },
    destroy: (asyncId) => {
      const meta = this._tracked[asyncId]
      if (meta) printMeta('destroy', meta)
      // delete meta for the event
      delete this._tracked[asyncId]
    },
    promiseResolve: (asyncId) => {
      const meta = this._tracked[asyncId]
      if (meta) printMeta('promiseResolve', meta)
    }
  })

  asyncHook.enable()

  function printMeta (eventName, meta) {
    console.log(`[${eventName}] asyncId=${meta.asyncId}, ` +
      `type=${meta.type}, pAsyncId=${meta.pAsyncId}, ` +
      `res type=${meta.res.constructor.name}`)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将在实验中使用它作为一个模块，所以让我们将它放在一个名为 verbose-hook.js 的文件中。为了简单起见，在我们的例子中，我们将主要使用计时器 API(准确地说，是 setTimeout()函数)。

首先，让我们看看单个计时器会发生什么:

```
require('./verbose-hook')(['Timeout'])

setTimeout(() => {
  console.log('Timeout happened')
}, 0)
console.log('Registered timeout') 
```

Enter fullscreen mode Exit fullscreen mode

该脚本将产生以下输出:

```
[init] asyncId=5, type=Timeout, pAsyncId=1, res type=Timeout
Registered timeout
[before] asyncId=5, type=Timeout, pAsyncId=1, res type=Timeout
Timeout happened
[after] asyncId=5, type=Timeout, pAsyncId=1, res type=Timeout
[destroy] asyncId=5, type=Timeout, pAsyncId=1, res type=Timeout 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，单个 setTimeout 操作的生命周期非常简单明了。它以一个(同步！)当异步操作(超时的回调)被添加到计时器队列中时，或者换句话说，当创建异步资源时，调用 init 侦听器。一旦要触发回调，就会触发 before 事件侦听器，然后在回调完成执行时触发 after 和 destroy 事件的侦听器。

您可能想知道，在嵌套操作的情况下会发生什么？我们来看看:

```
require('./verbose-hook')(['Timeout'])

setTimeout(() => {
  console.log('Timeout 1 happened')
  setTimeout(() => {
    console.log('Timeout 2 happened')
  }, 0)
  console.log('Registered timeout 2')
}, 0)
console.log('Registered timeout 1') 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本将产生一个更长的输出，看起来类似于这个:

```
[init] asyncId=5, type=Timeout, pAsyncId=1, res type=Timeout
Registered timeout 1
[before] asyncId=5, type=Timeout, pAsyncId=1, res type=Timeout
Timeout 1 happened
[init] asyncId=11, type=Timeout, pAsyncId=5, res type=Timeout
Registered timeout 2
[after] asyncId=5, type=Timeout, pAsyncId=1, res type=Timeout
[destroy] asyncId=5, type=Timeout, pAsyncId=1, res type=Timeout
[before] asyncId=11, type=Timeout, pAsyncId=5, res type=Timeout
Timeout 2 happened
[after] asyncId=11, type=Timeout, pAsyncId=5, res type=Timeout
[destroy] asyncId=11, type=Timeout, pAsyncId=5, res type=Timeout 
```

Enter fullscreen mode Exit fullscreen mode

输出显示嵌套的异步操作在异步钩子 API 中有直接的相关性。根 setTimeout 操作的 id(async id = 5)充当嵌套操作的父(或触发器)id(async id = 11)。此输出中显示的另一件有趣的事情是，根调用的销毁事件发生在嵌套销毁之前。这是因为 destroy listener 是在对应于异步操作的资源(在我们的例子中是 Timeout 对象)被销毁之后调用的。

与销毁事件相关的另一个重要注意事项是，在某些情况下，它可能根本不会被触发。官方文件是这样说的:

> 有些资源依赖垃圾收集来进行清理，因此如果引用传递给 init 的资源对象，则可能永远不会调用 destroy，从而导致应用程序中的内存泄漏。如果资源不依赖于垃圾收集，那么这就不是问题。

所以，如果你正在开发一个基于异步钩子的库，你需要考虑你的库可能引入的内存泄漏问题。

现在做点坏事怎么样？让我们试着创建一个超时，然后马上清除它，看看异步钩子将注册哪些事件:

```
require('./verbose-hook')(['Timeout'])

clearTimeout(
  setTimeout(() => {
    console.log('Timeout happened')
  }, 0)
)
console.log('Registered timeout') 
```

Enter fullscreen mode Exit fullscreen mode

该示例产生以下输出:

```
[init] asyncId=5, type=Timeout, pAsyncId=1, res type=Timeout
Registered timeout
[destroy] asyncId=5, type=Timeout, pAsyncId=1, res type=Timeout 
```

Enter fullscreen mode Exit fullscreen mode

尽管超时被立即取消，但在异步挂钩术语中，超时仍然会创建一个异步资源。因此，init 和 destroy 事件的侦听器仍然会被触发。此示例还显示，不能保证调用 after 和 before 事件。

到目前为止，我们还没有看到任何 promiseResolve 事件。这是因为我们在例子中没有使用任何本地承诺。先说最琐碎的例子:

```
require('./verbose-hook')(['PROMISE'])

Promise.resolve()
console.log('Registered Promise.resolve') 
```

Enter fullscreen mode Exit fullscreen mode

该脚本将以下内容输出到控制台:

```
[init] asyncId=5, type=PROMISE, pAsyncId=1, res type=PromiseWrap
[promiseResolve] asyncId=5, type=PROMISE, pAsyncId=1, res type=PromiseWrap
Registered Promise.resolve 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，在这个例子中，promiseResolve 监听器是在 Promise.resolve()函数执行期间同步运行的。正如 docs 提到的，当传递给 promise 构造函数的 Resolve 函数被调用时，promiseResolve 监听器将被触发(直接或通过其他解析 Promise 的方式)。在我们的例子中，由于 Promise.resolve()函数，resolve 函数被同步调用。

另一个结果是，在那些情况下，当用 then/catch 链构建承诺链时，promiseResolve(和其他侦听器)将被多次触发。为了说明这一点，让我们看看下面的例子(这次我们将使用 Promise.reject 来使这个例子与上一个例子有所不同):

```
require('./verbose-hook')(['PROMISE'])

Promise.reject()
  .catch(() => console.log('Promise.reject callback'))
console.log('Registered Promise.reject') 
```

Enter fullscreen mode Exit fullscreen mode

该脚本产生以下输出:

```
[init] asyncId=5, type=PROMISE, pAsyncId=1, res type=PromiseWrap
[promiseResolve] asyncId=5, type=PROMISE, pAsyncId=1, res type=PromiseWrap
[init] asyncId=8, type=PROMISE, pAsyncId=5, res type=PromiseWrap
Registered Promise.reject
[before] asyncId=8, type=PROMISE, pAsyncId=5, res type=PromiseWrap
Promise.reject callback
[promiseResolve] asyncId=8, type=PROMISE, pAsyncId=5, res type=PromiseWrap
[after] asyncId=8, type=PROMISE, pAsyncId=5, res type=PromiseWrap 
```

Enter fullscreen mode Exit fullscreen mode

正如所料，我们在这里看到了两个异步资源的层次结构。第一个(asyncId=5)对应于 Promise.reject()调用，而第二个(asyncId=8)代表连锁的 catch()调用。

至此，您应该已经理解了异步钩子 API 背后的主要原理。不要犹豫，用其他场景和[类型](https://nodejs.org/api/async_hooks.html#async_hooks_type)的事件做更多的实验。

现在，我们将讨论一些内部实现细节。

### 潜水深一点

**重要提示。**我在下面的所有链接中使用了来自节点主分支的最新提交之一，因此在过去/未来的版本中内部可能会有所不同。此外，本节中没有代码片段，所以如果您有兴趣查看源代码，请随时跟进这些链接。

如果你想通过读取节点源代码来理解异步钩子是如何实现的，那么首先要检查的是 [async_hooks](https://github.com/nodejs/node/blob/63b06551f4c51d8868f36b184a6539ebc51cf0b4/lib/internal/async_hooks.js) 模块本身。它定义了描述 createHook()函数返回的对象的[异步](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/lib/async_hooks.js#L44)类，以及所谓的 [JS 嵌入器 API](https://nodejs.org/api/async_hooks.html#async_hooks_javascript_embedder_api) 。后者允许你扩展 [AsyncResource](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/lib/async_hooks.js#L141) 类，这样你自己的资源的生命周期事件将由 Async Hooks API 处理。

如果你继续深入，你会发现[内部/异步钩子](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/lib/internal/async_hooks.js)模块。这个模块由公共模块使用，充当本机代码和 API 的 JS 部分之间的桥梁。异步钩子 API 的 C++部分由 [async_wrap](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/src/async_wrap.cc) 本机模块表示，该模块还定义了公共的 [C++嵌入器 API](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/src/async_wrap.cc#L678) 。本机 API 定义了 AsyncWrap 和 PromiseWrap 类，我们将在后面考虑这些类。所以，这三个模块定义了异步钩子 API 实现的主要部分。

让我们考虑一个调用链的具体例子，它发生在 init 监听器被触发之前。

在 JS 端，AsyncResource 类在构造函数中调用了 [emitInit](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/lib/async_hooks.js#L163) ()函数。这个函数是在内部/异步钩子模块中定义的。反过来，这个函数调用同一个模块的[eminitnative()](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/lib/internal/async_hooks.js#L131)函数。最后，这个函数同步遍历现有的活动钩子，并为每个钩子调用 init 侦听器。这就是为什么我们在实验中看到了 init 监听器的同步调用。

在 C++端，即对于本地异步资源，在异步资源构造函数中异步调用相同的 emitInitNative()函数(从本地代码的执行角度来看)。我觉得这次没有必要遍历整个调用链，所以相信我(或者你自己查一下)调用最终是发生在[emiasyncinit()](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/src/async_wrap.cc#L631)函数中的。

正如所料，Async Hooks API(即 AsyncWrap 本地类)被集成到所有标准 Node.js 模块中。例如，你可能会在本机模块内部发现 AsyncWrap，比如 [crypto](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/lib/internal/crypto/pbkdf2.js#L33) 和 [fs](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/src/node_file.cc#L101) ，以及其他许多地方。作为另一个例子，promiseResolve 事件监听器基于一个用于本地承诺的[钩子](https://github.com/nodejs/node/blob/e570ae79f5b1d74fed52d2aed7f014caaa0836dd/src/async_wrap.cc#L236)。

总之，任何发生在你的节点应用程序中的异步内容都可以通过异步钩子 API 进行列表。唯一的例外可能是一些第三方本机模块和它们周围的包装器。但是对于这些，您仍然可以使用嵌入式 API 将它们与异步挂钩集成在一起。

现在，随着我们对异步钩子的内部结构和原理有了更好的理解，让我们考虑一些可以用这个 API 解决的实际问题。

### 但是异步钩子有什么用呢？

正如我们已经知道的，异步钩子的第一个用例是[延续本地存储](https://github.com/Jeff-Lewis/cls-hooked) (CLS)，这是多线程世界中众所周知的线程本地存储概念的异步变体。简而言之，cls 挂钩库中的实现基于跟踪上下文对象和异步调用链之间的关联，异步调用链从特定的[函数](https://github.com/Jeff-Lewis/cls-hooked#namespacebindcallback-context)的执行或[事件发射器](https://github.com/Jeff-Lewis/cls-hooked#namespacebindemitteremitter)对象的生命周期开始。这里使用异步钩子 API 来监听异步操作，并在整个执行链中跟踪上下文。如果没有这个或类似的内置 API，您将不得不处理 Node.js 中所有异步 API 的大量猴子补丁。

第二个用例是关于分析和监控的。与[性能计时 API](https://nodejs.org/api/perf_hooks.html) 结合使用的异步钩子可以用来分析你的应用。这两个 API 允许收集应用程序中发生的异步操作的信息，并测量它们的持续时间。出于 web 应用程序监控的目的，我们可以构建一个中间件，以一种抽样的方式收集请求处理统计数据，即针对特定百分比的请求(不是所有请求)，从而最大限度地降低对应用程序的性能影响。这些信息可以被写入一个文件或流入一个独立的服务，并在以后以多种方式可视化，例如作为一个[火焰图](http://www.brendangregg.com/flamegraphs.html)。

作为构建在 Async Hooks API 之上的剖析工具的真实示例，我可以将它命名为 Bubbleprof 工具，它是 Clinic.js 的一部分。查看[这篇博客](https://clinicjs.org/blog/introducing-bubbleprof/)文章，了解更多关于 Bubbleprof 的信息。

希望这篇文章能让你更好地理解异步钩子。正如我们所看到的，Async Hooks API 是一个强大的开箱即用的特性，它允许以一种简洁的方式解决现实世界中的问题。

另外，如果你知道异步钩子的其他真实使用案例，请在下面的评论中描述它们。我很想听听这些。

* * *