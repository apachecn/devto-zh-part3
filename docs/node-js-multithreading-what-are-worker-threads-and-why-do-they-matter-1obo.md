# js 多线程:什么是工作线程，为什么它们很重要？

> 原文：<https://dev.to/bnevilleoneill/node-js-multithreading-what-are-worker-threads-and-why-do-they-matter-1obo>

[![](img/23e0a9f1a06fc8c248cbe5652bd051c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--91PclxeJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2A4VBxaeHaxTxjZiPbI7g3kw.jpeg)

自从发布了 [Node.js v10.5.0](https://nodejs.org/en/blog/release/v10.5.0/) 之后，又有了一个新的 *worker_threads* 模块。

这个新的工作线程模块到底是什么，我们为什么需要它？在这篇文章中，我们将讨论在 JavaScript 和 Node.js 中实现并发的历史原因，我们可能会发现的问题，当前的解决方案以及工作线程并行处理的未来。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 生活在单线程的世界

JavaScript 被认为是一种在浏览器中运行的单线程编程语言。被*单线程*意味着在同一个进程中一次只执行一组指令(这里指的是浏览器，或者只是现代浏览器中的当前选项卡)。

这使得实现和使用该语言的开发人员更加容易。JavaScript 最初是一种只用于向网页添加一些交互、表单验证等的语言。不需要复杂的多线程。

Node.js 的创始人瑞安·达尔(Ryan Dahl)将这一限制视为一个机会。他想实现一个基于异步 I/O 的服务器端平台，这意味着你不需要线程(这使事情变得容易得多)。并发可能是一个很难解决的问题。让多个线程访问同一个内存会产生很难重现和修复的争用情况。

### node . js 是单线程吗？

所以，我们的 Node.js 应用程序是单线程的，对吗？嗯，算是吧。

实际上，我们可以并行运行，但是我们不创建线程，也不同步线程。虚拟机和操作系统为我们并行运行 I/O，当需要将数据发送回我们的 JavaScript 代码时，JavaScript 部分是在单线程中运行的部分。

换句话说，一切都是并行运行的，除了我们的 JavaScript 代码。JavaScript 代码的同步块总是一次运行一个:

```
let flag = false
function doSomething() {
  flag = true
  // More code (that doesn't change `flag`)...

  // We can be sure that `flag` here is true.
  // There's no way other code block could have changed
  // `flag` since this block is synchronous
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们所做的只是异步 I/O，这就太好了。我们的代码由小部分同步块组成，这些同步块运行得很快，并将数据传递给文件和流。所以我们的 JavaScript 代码非常快，不会阻塞其他 JavaScript 代码的执行。等待 I/O 事件发生所花费的时间比执行 JavaScript 代码所花费的时间要多得多。让我们看一个简单的例子:

```
db.findOne('SELECT ... LIMIT 1', function(err, result) {
  if (err) return console.error(err)
  console.log(result)
})
console.log('Running query')
setTimeout(function() {
  console.log('Hey there')
}, 1000) 
```

Enter fullscreen mode Exit fullscreen mode

这个对数据库的查询可能需要一分钟，但是在调用查询后会立即显示“正在运行查询”消息。如果查询仍在运行，我们将在调用查询后一秒钟看到“嘿，你好”消息。我们的 Node.js 应用程序只是调用函数，并不阻止其他代码的执行。当查询完成时，它将通过回调得到通知，我们将收到结果。

### CPU 密集型任务

如果我们需要做同步的高强度工作，会发生什么？比如在大型数据集中进行内存中的复杂计算？那么我们可能会有一个同步的代码块，它花费了很多时间，并且会阻塞其余的代码。想象一下，一次计算需要 10s。如果我们正在运行一个 web 服务器，这意味着所有其他请求都会因为这个计算而被阻塞至少 10 秒钟。那是一场灾难。任何超过 100 毫秒的都可能太多。

JavaScript 和 Node.js 并不打算用于 CPU 密集型任务。由于 JavaScript 是单线程的，这将冻结浏览器中的 UI，并将 Node.js 中的任何 I/O 事件排队。

回到我们之前的例子。假设我们现在有一个返回几千个结果的查询，我们需要解密 JavaScript 代码中的值:

```
db.findAll('SELECT ...', function(err, results) {
  if (err) return console.error(err)

  // Heavy computation and many results
  for (const encrypted of results) {
    const plainText = decrypt(encrypted)
    console.log(plainText)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

一旦有结果，我们将在回拨中得到。然后，在我们的回调完成执行之前，不会执行其他 JavaScript 代码。通常，正如我们之前所说的，代码很少并且足够快，但是在这种情况下，我们有许多结果，并且我们需要对它们进行繁重的计算。这可能需要几秒钟，在此期间，任何其他 JavaScript 执行都将排队，这意味着，如果我们在同一个应用程序中运行服务器，我们可能会在此期间阻塞所有用户。

### 为什么 JavaScript 中永远不会有线程

因此，在这一点上，许多人会认为有人需要在 Node.js 核心中添加一个新模块，并允许我们创建和同步线程。应该就是这样吧？遗憾的是，在 Node.js 这种成熟的服务器端平台上，我们没有很好的方法来解决这个用例。

好吧，如果我们添加线程，那么我们正在改变语言的本质。我们不能仅仅将线程作为一组新的可用类或函数来添加。我们需要改变语言。支持多线程的语言有诸如“synchronized”这样的关键字，以使线程能够协作。例如，在 Java 中[甚至一些数值类型也不是原子的](https://dzone.com/articles/longdouble-are-not-atomic-in-java)，这意味着如果你不同步它们的访问，你可能最终让两个线程改变一个变量的值，并且导致在两个线程都访问它之后，变量有几个字节被一个线程改变，几个字节被另一个线程改变，因此，不会产生任何有效值。

### 天真的解决方案:滴答，滴答，滴答

Node.js 不会计算事件队列中的下一个代码块，直到上一个代码块执行完毕。所以我们可以做的一件简单的事情是将我们的代码分割成更小的同步代码块，并调用 *setImmediate(callback)* 来告诉 Node.js 我们已经完成了，它可以继续执行队列中的未决事务。

它可以在事件循环的下一次迭代或“滴答”时继续。让我们看看如何重构一些代码来利用这一点。假设我们有一个要处理的大型数组，数组中的每一项都需要 CPU 密集型处理:

```
const arr = [/*large array*/]
for (const item of arr) {
  // do heavy stuff for each item on the array
}
// code that runs after the whole array is executed 
```

Enter fullscreen mode Exit fullscreen mode

就像我们之前说过的，如果我们这样做，整个数组的处理将会花费太多的时间，并且会阻塞其余的 JavaScript 执行。所以让我们把它分成更小的块，并使用 *setImmediate(回调)* :

```
const crypto = require('crypto')

const arr = new Array(200).fill('something')
function processChunk() {
  if (arr.length === 0) {
    // code that runs after the whole array is executed
  } else {
    console.log('processing chunk');
    // pick 10 items and remove them from the array
    const subarr = arr.splice(0, 10)
    for (const item of subarr) {
      // do heavy stuff for each item on the array
      doHeavyStuff(item)
    }
    // Put the function back in the queue
    setImmediate(processChunk)
  }
}

processChunk()

function doHeavyStuff(item) {
  crypto.createHmac('sha256', 'secret').update(new Array(10000).fill(item).join('.')).digest('hex')
}

// This is just for confirming that we can continue
// doing things
let interval = setInterval(() => {
  console.log('tick!')
  if (arr.length === 0) clearInterval(interval)
}, 0) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们每次处理 10 个项目，并调用 *setImmediate(callback)* ,因此如果程序需要做其他事情，它将在这 10 个项目之间完成。我添加了一个 setInterval()来演示这一点。

如你所见，代码变得越来越复杂。很多时候算法要比这个复杂得多，所以很难知道在哪里放置 *setImmediate()* 来找到一个好的平衡。此外，现在的代码是异步的，如果我们依赖第三方库，我们可能无法将执行分成更小的块。

### 后台流程

因此 *setImmediate()* 对于一些简单的用例来说可能是可以的，但是它远不是一个理想的解决方案。此外，我们没有线程(原因很好),我们也不想修改语言。我们可以不用线程进行并行处理吗？是的，我们需要的只是某种后台处理:一种运行带有输入的任务的方式，它可以使用所需的任何数量的 CPU 和时间，并将结果返回给主应用程序。大概是这样:

```
// Runs `script.js` in a new environment without sharing memory.
const service = createService('script.js')
// We send an input and receive an output
service.compute(data, function(err, result) {
  // result available here
}) 
```

Enter fullscreen mode Exit fullscreen mode

事实上，我们已经可以在 Node.js 中进行后台处理了。我们可以[派生出流程](https://nodejs.org/api/child_process.html#child_process_child_process_fork_modulepath_args_options)并使用消息传递来完成。主进程可以通过发送和接收事件与子进程通信。没有共享内存。所有交换的数据都是“克隆”的，这意味着在一端改变数据不会在另一端改变它。就像 HTTP 响应一样，一旦您发送了它，另一端就只有它的副本。如果我们不共享内存，我们就没有竞争条件，也不需要线程。问题解决了！

好吧，等一下。这是一个解决方案，但不是理想的解决方案。就资源而言，派生进程是一个昂贵的进程。而且很慢。这意味着使用大量内存从头运行一个新的虚拟机，因为进程不共享内存。我们可以重用相同的分叉流程吗？当然，但是发送将在分叉流程中同步执行的不同繁重工作负载有两个问题:

*   是的，你没有阻塞主应用程序，但是分叉的进程一次只能处理一个任务。如果你有两个任务，一个需要 10 秒，另一个需要 1 秒(按这个顺序)，等待 10 秒来执行第二个任务是不理想的。因为我们正在分叉进程，所以我们希望利用操作系统和我们机器的所有核心的调度。就像你可以同时听音乐和浏览互联网一样，你可以分叉两个进程，并行执行所有的任务。
*   此外，如果一个任务使流程崩溃，它将使发送到同一个流程的所有任务都没有完成。

为了解决这些问题，我们需要多个分支，而不仅仅是一个，但是我们需要限制分支进程的数量，因为每个分支进程都将所有虚拟机代码复制到内存中，这意味着每个进程只有几兆字节，启动时间也不短。因此，像数据库连接一样，我们需要一个随时可用的进程池，在每个进程池中一次运行一个任务，并在任务完成后重用该进程。这看起来实现起来很复杂，确实如此！让我们用[工人农场](https://www.npmjs.com/package/worker-farm)来帮助我们:

```
// main app
const workerFarm = require('worker-farm')
const service = workerFarm(require.resolve('./script'))

service('hello', function (err, output) {
  console.log(output)
})

// script.js
// This will run in forked processes
module.exports = (input, callback) => {
  callback(null, input + '  ' + world)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 问题解决了？

问题解决了吗？是的，我们已经解决了这个问题，但是我们仍然使用比多线程解决方案多得多的内存。与分叉进程相比，线程在资源方面仍然是非常轻量级的。而这就是工作者线程诞生的原因！

工作线程有独立的上下文。它们使用消息传递与主进程交换信息，因此我们避免了线程所具有的竞争条件问题！但是它们确实生活在同一个进程中，所以它们使用的内存要少得多。

你可以和工作线程共享内存。您可以传递专门用于该目的的 SharedArrayBuffer 对象。只有在需要处理大量数据的 CPU 密集型任务时，才使用它们。它们允许您避免数据的序列化步骤。

### 让我们开始使用工作线程吧！

如果您运行 Node.js v10.5.0 或更高版本，您现在就可以开始使用 worker threads，但是请记住，这是一个可能会发生变化的实验性 API。事实上，它在默认情况下是不可用的:您需要在调用 Node.js 时使用 _ — experimental-worker_ 来启用它。

此外，请记住，创建一个工作线程(就像任何语言中的线程一样)即使比派生一个进程便宜得多，也会根据您的需要使用太多的资源。在这种情况下，医生建议你创建一个工人库。您可以在 NPM 寻找一个通用的池实现或一个特定的池实现，而不是创建您自己的池实现。

但是让我们看一个简单的例子。首先，我们将实现主文件，在这里我们将创建一个工作线程并给它一些数据。API 是事件驱动的，但我将把它包装成一个承诺，在从 Worker 收到的第一条消息中解决:

```
// index.js
// run with node --experimental-worker index.js on Node.js 10.x
const { Worker } = require('worker_threads')

function runService(workerData) {
  return new Promise((resolve, reject) => {
    const worker = new Worker('./service.js', { workerData });
    worker.on('message', resolve);
    worker.on('error', reject);
    worker.on('exit', (code) => {
      if (code !== 0)
        reject(new Error(`Worker stopped with exit code ${code}`));
    })
  })
}

async function run() {
  const result = await runService('world')
  console.log(result);
}

run().catch(err => console.error(err)) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这就像将文件名作为参数和我们希望工人处理的数据传递一样简单。记住这个数据是*克隆的*，它不在任何共享内存中。然后，我们通过监听“消息”事件来等待工作线程向我们发送消息。

现在，我们需要实现服务。

```
const { workerData, parentPort } = require('worker_threads')

// You can do any heavy stuff here, in a synchronous way
// without blocking the "main thread"
parentPort.postMessage({ hello: workerData }) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们需要两样东西:主应用程序发送给我们的 workerData，以及将信息返回给主应用程序的方法。这是通过具有 postMessage 方法的 parentPort 完成的，我们将在该方法中传递处理结果。

就是这样！这是最简单的例子，但是我们可以构建更复杂的东西，例如，如果我们需要提供反馈，我们可以从工作线程发送多条消息来指示执行状态。或者我们能否发送部分结果。例如，假设您正在处理数以千计的图像，可能您想要为每一个处理过的图像发送一条消息，但是您不想等到所有的图像都被处理完。

为了运行这个示例，如果您在 Node.js 10.x:
中，请记住使用 experimental-worker 标志

```
node --experimental-worker index.js 
```

Enter fullscreen mode Exit fullscreen mode

如需更多信息，请查看 [worker_threads](https://nodejs.org/docs/latest-v10.x/api/worker_threads.html) 文档。

### 网络工作者呢？

也许你听说过网络工作者。它们是更成熟的网络应用程序接口(T1)和现代浏览器支持的 T2(T3)。API 不一样是因为需求和技术条件不一样，但是可以解决浏览器运行时类似的问题。如果您正在进行加密、压缩/解压缩、图像处理、计算机视觉(例如人脸识别)等操作，它会非常有用。在您的 web 应用程序中。

### 结论

如果您需要在 Node.js 应用程序中执行 CPU 密集型任务，Worker threads 是一个很有前途的实验模块。这就像没有共享内存的线程，因此没有它们引入的潜在竞争条件。由于它仍然是实验性的，我会在使用它之前等待，我会使用 [worker-farm](https://www.npmjs.com/package/worker-farm) (或类似的模块)来做后台处理。将来，一旦你的程序足够成熟，它应该很容易移植到工作线程上！

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *