# NodeJS 日志记录正确

> 原文：<https://dev.to/itnext/nodejs-logging-made-right-4b2c>

> 我现在有了一个闪亮的新博客。阅读这篇文章，了解 https://blog.goncharov.page/nodejs-logging-made-right 的最新动态

当你想到登录 NodeJS 的时候，最困扰你的是什么？如果你问我，我会说缺乏创建跟踪 id 的行业标准。在本文中，我们将概述如何创建这些跟踪 id(这意味着我们将简要研究延续本地存储又称为 [CLS](https://github.com/jeff-lewis/cls-hooked) 如何工作),并深入探讨如何利用[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)使其与任何日志记录器一起工作。

## 为什么 NodeJS 中每个请求都有一个 trace ID 甚至是一个问题？

嗯，在使用多线程并为每个请求生成一个新线程的平台上，有一种叫做[线程本地存储的东西，也称为 TLS](https://en.wikipedia.org/wiki/Thread-local_storage) ，它允许在一个线程内保存任何任意数据。如果您有一个本地 API 来完成这项工作，那么为每个请求生成一个随机 ID 是非常简单的，将它放在 TLS 中，稍后在您的控制器或服务中使用它。NodeJS 是怎么回事？
如你所知，NodeJS 是一个单线程平台(不再是真的，因为我们现在有工人，但这不会改变大局)，这使得 TLS 过时了。NodeJS 不是操作不同的线程，而是在同一个线程中运行不同的回调(如果你感兴趣的话，在 NodeJS 中有一个关于事件循环的的[系列文章), NodeJS 为我们提供了一种唯一地识别这些回调并追踪它们之间关系的方法。](https://jsblog.insiderattack.net/event-loop-and-the-big-picture-nodejs-event-loop-part-1-1cb67a182810)

回到过去(v0.11.11 ),我们有 [addAsyncListener](https://nodejs.org/docs/v0.11.11/api/process.html#process_async_listeners) ,它允许我们跟踪异步事件。基于它 [Forrest Norvell](https://github.com/othiym23) 构建了[延续本地存储的第一个实现，又名 CLS](https://github.com/othiym23/node-continuation-local-storage) 。我们不打算讨论 CLS 的实现，因为作为开发者，我们在 v0.12 中已经去掉了这个 API。

在 NodeJS 8 之前，我们没有正式的方法来连接 NodeJS 的异步事件处理。最后，NodeJS 8 通过 [async_hooks](https://nodejs.org/docs/latest-v11.x/api/async_hooks.html) 赋予了我们失去的能力(如果你想更好地理解 async_hooks，看看[这篇文章](https://itnext.io/a-pragmatic-overview-of-async-hooks-api-in-node-js-e514b31460e9))。这就把我们带到了基于现代 async_hooks 的 CLS - [cls-hooked](https://github.com/Jeff-Lewis/cls-hooked) 的实现。

## CLS 概述

以下是 CLS 工作原理的简化流程:

[![CLS overview](img/b7b2580c27231d87ff6e835ff7db2306.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eBHoYCsr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zb36bsngpi11dl2189d9.jpg)

让我们一步一步地分解它:

1.  比方说，我们有一个典型的 web 服务器。首先，我们必须创建一个 CLS 命名空间。在我们应用程序的整个生命周期中一次。
2.  其次，我们必须配置一个中间件，为每个请求创建一个新的 CLS 上下文。为了简单起见，让我们假设这个中间件只是一个回调函数，在接收到一个新请求时被调用。
3.  所以当一个新的请求到达时，我们调用回调函数。
4.  在该函数中，我们创建一个新的 CLS 上下文(方法之一是使用[运行](https://github.com/jeff-lewis/cls-hooked#namespaceruncallback) API 调用)。
5.  此时，CLS 通过[当前执行 ID](https://nodejs.org/api/async_hooks.html#async_hooks_async_hooks_executionasyncid) 将新的上下文放入上下文映射中。
6.  每个 CLS 命名空间都有`active`属性。在这个阶段，CLS 将`active`分配给上下文。
7.  在上下文内部，我们调用一个异步资源，比方说，我们从数据库请求一些数据。我们向该调用传递一个回调，该调用将在对数据库的请求完成后运行。
8.  [init](https://nodejs.org/api/async_hooks.html#async_hooks_init_asyncid_type_triggerasyncid_resource) 为新的异步操作触发异步挂钩。它通过 async ID 将当前上下文添加到上下文映射中(将其视为新异步操作的标识符)。
9.  因为我们在第一个回调函数中没有更多的逻辑，所以它退出了，有效地结束了我们的第一个异步操作。
10.  [在](https://nodejs.org/api/async_hooks.html#async_hooks_after_asyncid)之后，第一次回调触发异步钩子。它将名称空间上的活动上下文设置为`undefined`(这并不总是正确的，因为我们可能有多个嵌套上下文，但对于最简单的情况来说，这是正确的)。
11.  [销毁](https://nodejs.org/api/async_hooks.html#async_hooks_destroy_asyncid)钩子在第一次操作时被触发。它通过上下文的异步 ID(与第一次回调的当前执行 ID 相同)从上下文映射中删除上下文。
12.  对数据库的请求已经完成，我们的第二次回调即将被触发。
13.  此时[先于](https://nodejs.org/api/async_hooks.html#async_hooks_before_asyncid)异步挂钩进场。它的当前执行 ID 与第二个操作(数据库请求)的异步 ID 相同。它将名称空间的`active`属性设置为通过其当前执行 ID 找到的上下文。这是我们之前创造的环境。
14.  现在我们进行第二次回调。在里面运行一些业务逻辑。在该函数中，我们可以通过键从 CLS 中获取任何值，它将返回在我们之前创建的上下文中通过键找到的任何值。
15.  假设函数返回的请求处理结束。
16.  [在](https://nodejs.org/api/async_hooks.html#async_hooks_after_asyncid)之后，第二次回调触发了异步钩子。它将名称空间上的活动上下文设置为`undefined`。
17.  第二个异步操作触发了钩子。它通过异步 ID 从上下文映射中删除我们的上下文，使其完全为空。
18.  由于我们不再持有对上下文对象的任何引用，我们的垃圾收集器释放与之相关的内存。

这是在引擎盖下发生的事情的简化版本，然而它涵盖了所有主要步骤。如果你想更深入地挖掘，你可以看一看[源代码](https://github.com/Jeff-Lewis/cls-hooked/blob/master/context.js)。不到 500 行。

## 生成跟踪 id

所以，一旦我们对 CLS 有了全面的了解，让我们想想如何利用它为我们自己谋福利。我们可以做的一件事是创建一个中间件，它将每个请求包装在一个上下文中，生成一个随机标识符，并通过键`traceID`将其放入 CLS。后来，在我们数不清的控制器和服务中，我们可以从 CLS 那里得到那个标识符。

对于 [express](https://github.com/expressjs/express) 来说，这个中间件可以是这样的:

```
const cls = require('cls-hooked')
const uuidv4 = require('uuid/v4')

const clsNamespace = cls.createNamespace('app')

const clsMiddleware = (req, res, next) => {
  // req and res are event emitters. We want to access CLS context inside of their event callbacks
  clsNamespace.bind(req)
  clsNamespace.bind(res)

  const traceID = uuidv4()

  clsNamespace.run(() => {
    clsNamespace.set('traceID', traceID)

    next()
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在我们的控制器中，我们可以得到如下生成的跟踪 ID:

```
const controller = (req, res, next) => {
  const traceID = clsNamespace.get('traceID')
} 
```

Enter fullscreen mode Exit fullscreen mode

除非我们将这个跟踪 ID 添加到日志中，否则它没有多大用处。

让我们把它加到我们的[温斯顿](https://github.com/winstonjs/winston)里。

```
const { createLogger, format, transports } = require('winston')

const addTraceId = printf((info) => {
  let message = info.message
  const traceID = clsNamespace.get('taceID')
  if (traceID) {
    message = `[TraceID: ${traceID}]: ${message}`
  }
  return message
})

const logger = createLogger({
  format: addTraceId,
  transports: [new transports.Console()],
}) 
```

Enter fullscreen mode Exit fullscreen mode

好吧，如果所有的记录器都支持函数形式的格式化程序(他们中的许多人没有这么做是有原因的),那么这篇文章就不会存在了。那么，如何给我心爱的 [pino](https://github.com/pinojs/pino) 添加一个追踪 ID 呢？[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)前来救援！

## 结合代理和 CLS

代理是一个包装原始对象的对象，允许我们在某些情况下覆盖它的行为。这些情况的列表(它们实际上被称为陷阱)是有限的，你可以在这里看一下整套[，但是我们只对陷阱](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy#Methods_of_the_handler_object)[到](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/get)感兴趣。它为我们提供了拦截财产访问的能力。这意味着如果我们有一个对象`const a = { prop: 1 }`并把它包装在一个代理中，用`get` trap 我们可以为`a.prop`返回任何我们想要的东西。

因此，我们的想法是为每个请求生成一个随机的跟踪 ID，并用这个跟踪 ID 创建一个[子 pino 记录器](https://github.com/pinojs/pino/blob/master/docs/child-loggers.md)，并把它放在 CLS。然后，我们可以用一个代理包装我们的原始日志记录器，如果找到一个，它会将所有日志记录请求重定向到 CLS 的子日志记录器，否则继续使用原始日志记录器。

在这种情况下，我们的代理可能如下所示:

```
const pino = require('pino')

const logger = pino()
const loggerCls = new Proxy(logger, {
  get(target, property, receiver) {
    // Fallback to our original logger if there is no child logger in CLS
    target = clsNamespace.get('loggerCls') || target
    return Reflect.get(target, property, receiver)
  },
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们的中间件会变成这样:

```
const cls = require('cls-hooked')
const uuidv4 = require('uuid/v4')

const clsMiddleware = (req, res, next) => {
  // req and res are event emitters. We want to access CLS context inside of their event callbacks
  clsNamespace.bind(req)
  clsNamespace.bind(res)

  const traceID = uuidv4()
  const loggerWithTraceId = logger.child({ traceID })

  clsNamespace.run(() => {
    clsNamespace.set('loggerCls', loggerWithTraceId)

    next()
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以这样使用日志记录器:

```
const controller = (req, res, next) => {
  loggerCls.info('Long live rocknroll!')
  // Logs something like
  // {"level":30,"time":1551385666046,"msg":"Long live rocknroll!","pid":25,"hostname":"eb6a6c70f5c4","traceID":"9ba393f0-ec8c-4396-8092-b7e4b6f375b5","v":1}
} 
```

Enter fullscreen mode Exit fullscreen mode

## [cls-proxify](https://github.com/aigoncharov/cls-proxify)

基于上述想法，一个名为 cls-proxify 的小型库被创建。它集成了 [express](https://github.com/expressjs/express) 、 [koa](https://github.com/koajs/koa) 和 [fastify](https://github.com/fastify/fastify) 开箱即用。
它不仅将`get`陷印应用于原始对象，还应用于[许多其他对象](https://github.com/aigoncharov/cls-proxify#does-it-work-only-for-loggers)。所以有无数可能的应用。你可以代理函数调用，类构造，你只受到你的想象力的限制！

希望你已经找到了对你的项目有用的东西。请随时将您的反馈传达给我！我非常感谢任何批评和问题。