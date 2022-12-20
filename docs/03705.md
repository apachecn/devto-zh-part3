# 在无服务器函数中使用 Node.js 工作线程节省金钱和时间

> 原文：<https://dev.to/ibmdeveloper/saving-money-and-time-with-node-js-worker-threads-in-serverless-functions-d0n>

Node.js v12 是上个月发布的[。这个新版本包括对默认启用的](https://foundation.nodejs.org/announcements/2019/04/24/node-js-foundation-and-js-foundation-merge-to-form-openjs-foundation-2)[工作线程](https://nodejs.org/api/worker_threads.html)的支持。Node.js [工作线程](https://nodejs.org/api/worker_threads.html)使得使用线程并行执行 JavaScript 代码变得简单。👏👏👏

这对于 CPU 密集型工作负载的 Node.js 应用程序非常有用。使用工作线程，JavaScript 代码可以使用多个 CPU 内核并发执行。与非工作线程版本相比，这减少了执行时间。

如果无服务器平台在多核环境中提供 Node.js v12，函数可以使用该特性来减少执行时间，从而降低成本。根据工作负载，函数可以利用所有可用的 CPU 内核来并行处理工作，而不是同时执行更多的函数。💰💰💰

在这篇博文中，我将解释如何在无服务器函数中使用工作线程。我将使用[IBM Cloud Functions](https://cloud.ibm.com/openwhisk)([Apache open whish](http://openwhisk.incubator.apache.org/))作为示例平台，但这种方法适用于任何支持 Node.js v12 和多核 CPU 运行时环境的无服务器平台。

## IBM 云函数(Apache OpenWhisk)中的 Node.js v12

博客文章的这一部分专门讨论在 IBM 云函数上使用新的 [Node.js v12 运行时](https://github.com/apache/incubator-openwhisk-runtime-nodejs)(由[Apache open whish](http://openwhisk.incubator.apache.org/)提供支持)。如果您使用不同的无服务器平台，请随意跳到下一节…

我最近[一直在](https://github.com/apache/incubator-openwhisk-runtime-nodejs/pull/126)为 Apache OpenWhisk 添加 Node.js v12 运行时。

Apache OpenWhisk 使用 [Docker 容器](https://hub.docker.com/u/openwhisk)作为无服务器功能的运行时环境。所有的运行时映像都被维护在每种支持语言的单独的存储库中，例如 [Node.js](https://github.com/apache/incubator-openwhisk-runtime-nodejs) 、 [Java](https://github.com/apache/incubator-openwhisk-runtime-java) 、 [Python](https://github.com/apache/incubator-openwhisk-runtime-python) 等。当存储库更新时，运行时映像被自动构建并推送到 [Docker Hub](https://hub.docker.com/r/openwhisk/) 。

### node.js v12 运行时图像

这里是用于将新的 Node.js v12 运行时映像添加到 Apache OpenWhisk 的 PR 。这导致下面的[运行时映像](https://hub.docker.com/r/openwhisk/action-nodejs-v12)被导出到 Docker Hub: `openwhisk/action-nodejs-v12`。

在 Apache OpenWhisk 中让这个映像作为本机运行时可用需要对项目的运行时清单进行[上游变更](https://github.com/apache/incubator-openwhisk/pull/4472)。在这之后，开发人员将能够使用`--kind` CLI 标志来选择这个运行时版本。

```
ibmcloud wsk action create action_name action.js --kind nodejs:12 
```

[IBM 云功能](http://cloud.ibm.com/openwhisk)由 [Apache OpenWhisk](http://openwhisk.incubator.apache.org/) 提供支持。它将最终获得上游项目的变更，以包含这个新的运行时版本。在此之前，Docker 支持允许在将这个新的运行时嵌入平台之前使用它。

```
ibmcloud wsk action create action_name action.js --docker openwhisk/action-nodejs-v12 
```

### 举例

这个 Apache OpenWhisk 操作返回运行时环境中使用的 Node.js 版本。

```
function main () {
  return {
    version: process.version
  }
} 
```

使用 Node.js v12 运行时映像在 IBM Cloud Functions 上运行这段代码，使我们能够确认新的 Node.js 版本是可用的。

```
$ ibmcloud wsk action create nodejs-v12 action.js --docker openwhisk/action-nodejs-v12
ok: created action nodejs-v12
$ ibmcloud wsk action invoke nodejs-v12 --result
{
    "version": "v12.1.0"
} 
```

## 无服务器函数中的工作线程

[这是一篇很好的介绍 Workers Threads 的博文](https://medium.com/@Trott/using-worker-threads-in-node-js-80494136dbb6)。它使用一个生成素数的例子作为 CPU 密集型任务进行基准测试。将单线程版本的性能与多线程版本进行比较——性能的提高取决于所使用的线程数量(取决于可用的 CPU 内核数量)。

这段代码可以移植到一个无服务器的函数中运行。使用不同的输入值和线程数运行将允许性能改进的基准测试。

### 非工人版

下面是用于生成素数的无服务器函数的示例代码。它不使用工作线程。它将在 Node.js 流程的主[事件循环](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)上运行。这意味着它将只利用一个线程(因此只有一个 CPU 内核)。

```
'use strict';

const min = 2

function main(params) {
  const { start, end } = params
  console.log(params)
  const primes = []
  let isPrime = true;
  for (let i = start; i < end; i++) {
    for (let j = min; j < Math.sqrt(end); j++) {
      if (i !== j && i%j === 0) {
        isPrime = false;
        break;
      }
    }
    if (isPrime) {
      primes.push(i);
    }
    isPrime = true;
  }

  return { primes }
} 
```

### 移植代码使用工作线程

下面是使用工作线程的质数计算代码。将总输入范围除以工作线程的数量会生成单独的线程输入值。产生工作线程并向其传递分块输入范围。线程计算素数，然后将结果发送回父线程。

审查代码以开始将其转换为无服务器函数，我意识到在无服务器环境中运行这段代码有两个问题:**工作线程初始化**和**最佳工作线程计数**。

#### 如何初始化工作线程？

这就是现有源代码[初始化工作线程](https://nodejs.org/dist/latest-v12.x/docs/api/worker_threads.html#worker_threads_new_worker_filename_options)的方式。

```
 threads.add(new Worker(__filename, { workerData: { start: myStart, range }})); 
```

*`__filename`是 Node.js 中一个特殊的全局变量，包含当前执行的脚本文件路径。*

这意味着工作线程将使用当前执行脚本的副本进行初始化。Node.js 提供了一个特殊的变量来指示脚本是在父线程中执行还是在子线程中执行。这可以用于分支脚本逻辑。

**那么，这有什么问题呢？**

在 Apache OpenWhisk Node.js 运行时，动作源文件被动态地[导入](https://github.com/apache/incubator-openwhisk-runtime-nodejs/blob/master/core/nodejsActionBase/runner.js#L61-L79)到运行时环境中。用于启动 Node.js 运行时进程的脚本是针对[平台处理程序](https://github.com/apache/incubator-openwhisk-runtime-nodejs/blob/master/core/nodejsActionBase/runner.js)的，而不是动作源文件。这意味着`__filename`变量不指向动作源文件。

通过将无服务器函数处理程序和工作线程代码分离到单独的文件中，可以解决此问题。工作线程可以通过引用工作线程脚本源文件而不是当前执行的脚本名称来启动。

```
 threads.add(new Worker("./worker.js", { workerData: { start: myStart, range }})); 
```

#### 有多少工作线程？

下一个要解决的问题是使用多少工作线程。为了最大化并行处理能力，每个 CPU 内核都应该有一个工作线程。这是可以并发运行的最大线程数。

Node.js 使用`os.cpus()` [函数](https://nodejs.org/api/os.html#os_os_cpus)为运行时环境提供 CPU 信息。结果是一个对象数组(每个逻辑 CPU 内核一个)，其中包含模型信息、处理速度和经过的处理时间。该数组的长度将决定使用的工作线程的数量。这确保了工作线程的数量总是与可用的 CPU 内核相匹配。

```
const threadCount = os.cpus().length 
```

### 工作者线程版本

这是素数生成算法的无服务器版本，它使用工作线程。

代码被分成两个文件- `primes-with-workers.js`和`worker.js`。

#### 质数与工人. js

[该文件](https://gist.github.com/jthomas/154a039d52b97d5ed19d4ddac3ff9f43)包含平台使用的无服务器函数处理程序。输入范围(基于`min`和`max`动作参数)根据工作线程的数量划分成块。处理函数为每个块创建一个工作线程，并等待带有结果的消息。一旦检索到所有结果，它将返回所有这些素数作为调用结果。

```
'use strict';

const { Worker } = require('worker_threads');
const os = require('os')
const threadCount = os.cpus().length

const compute_primes = async (start, range) => {
  return new Promise((resolve, reject) => {
    let primes = []
    console.log(`adding worker (${start} => ${start + range})`)
    const worker = new Worker('./worker.js', { workerData: { start, range }})

    worker.on('error', reject)
    worker.on('exit', () => resolve(primes))
    worker.on('message', msg => {
      primes = primes.concat(msg)
    })
  })
}

async function main(params) {
  const { min, max } = params
  const range = Math.ceil((max - min) / threadCount)
  let start = min < 2 ? 2 : min
  const workers = []

  console.log(`Calculating primes with ${threadCount} threads...`);

  for (let i = 0; i < threadCount - 1; i++) {
    const myStart = start
    workers.push(compute_primes(myStart, range))
    start += range
  }

  workers.push(compute_primes(start, max - start))

  const primes = await Promise.all(workers)
  return { primes: primes.flat() }
}

exports.main = main 
```

#### workers.js

[这是在工作者线程中使用的脚本](https://gist.github.com/jthomas/154a039d52b97d5ed19d4ddac3ff9f43#file-workers-js)。`workerData`值用于接收数字范围以搜索质数。使用`postMessage`函数将素数发送回父线程。因为这个脚本只在工作线程中使用，所以它需要使用`isMainThread`值来检查它是子进程还是父进程。

```
'use strict';
const { Worker, isMainThread, parentPort, workerData } = require('worker_threads');

const min = 2

function generatePrimes(start, range) {
  const primes = []
  let isPrime = true;
  let end = start + range;
  for (let i = start; i < end; i++) {
    for (let j = min; j < Math.sqrt(end); j++) {
      if (i !== j && i%j === 0) {
        isPrime = false;
        break;
      }
    }
    if (isPrime) {
      primes.push(i);
    }
    isPrime = true;
  }

  return primes
}

const primes = generatePrimes(workerData.start, workerData.range);
parentPort.postMessage(primes) 
```

#### package.json

从 zip 文件部署的源文件也需要在归档中包含一个`package.json`文件。`main`属性用于确定作为导出的包模块导入的脚本。

```
{  "name":  "worker_threads",  "version":  "1.0.0",  "main":  "primes-with-workers.js",  } 
```

## 性能对比

使用相同的输入参数运行这两个函数可以进行执行时间比较。Worker Threads 版本的性能应该会提高一个与可用 CPU 内核成比例的系数。减少执行时间也意味着降低无服务器平台的成本。

### 非工作人员表现

从非工作线程源代码创建一个新的无服务器函数(`primes`)，使用 Node.js v12 运行时，我可以用小值测试来检查正确性。

```
$ ibmcloud wsk action create primes primes.js --docker openwhisk/action-nodejs-v12
ok: created action primes
$ ibmcloud wsk action invoke primes --result -p start 2 -p end 10
{
    "primes": [ 2, 3, 5, 7 ]
} 
```

使用示例输入值，10，000，000 似乎是一个有用的基准值。对于单线程版本，这需要足够长的时间才能从并行性中获益。

```
$ time ibmcloud wsk action invoke primes --result -p start 2 -p end 10000000 > /dev/null

real    0m35.151s
user    0m0.840s
sys 0m0.315s 
```

使用简单的单线程算法，这个无服务器的函数需要大约 35 秒来计算高达一千万的素数。

### 工作者线程性能

使用 Node.js v12 运行时从基于 worker threads 的源代码创建一个新的无服务器函数，这允许我验证它对于小输入值的工作情况。

```
$ ibmcloud wsk action create primes-workers action.zip --docker openwhisk/action-nodejs-v12
ok: created action primes-workers
$ ibmcloud wsk action invoke primes-workers --result -p min 2 -p max 10
{
    "primes": [ 2, 3, 5, 7 ]
} 
```

好啊，成功了。

使用 10，000，000 的`max`参数调用函数允许我们针对非工作人员版本的代码进行基准测试。

```
$ time ibmcloud wsk action invoke primes-workers --result -p min 2 -p max 10000000 --result > /dev/null

real    0m8.863s
user    0m0.804s
sys 0m0.302s 
```

**workers 版本只需要单线程版本时间的 25%!**

这是因为 IBM Cloud Functions 的运行时环境提供了对四个 CPU 内核的访问。与其他平台不同，CPU 内核与内存分配无关。同时利用所有可用的 CPU 内核使算法运行速度提高了 4 倍。由于无服务器平台根据执行时间收费，减少执行时间也意味着降低成本。

工作线程版本的成本也比单线程版本低 75%!

## 结论

[Node.js v12](https://foundation.nodejs.org/announcements/2019/04/24/node-js-foundation-and-js-foundation-merge-to-form-openjs-foundation-2) 发布于 2019 年 4 月。这个版本支持默认启用的[工作线程](https://nodejs.org/api/worker_threads.html)(而不需要可选的运行时标志)。在 Node.js 应用程序中使用多个 CPU 内核从未如此简单！

具有 CPU 密集型工作负载的 Node.js 应用程序可以利用这个特性来减少执行时间。由于无服务器平台根据执行时间收费，这对于 Node.js 无服务器函数特别有用。利用多个 CPU 内核不仅可以提高性能，还可以降低成本。

PRs 已经被[打开](https://github.com/apache/incubator-openwhisk/pull/4472)以启用 Node.js v12 作为 Apache OpenWhisk 项目的内置运行时。新运行时版本的 Docker [映像](https://hub.docker.com/r/openwhisk/action-nodejs-v12)已经在 Docker Hub 上发布。这意味着它可以直接用于任何 Apache OpenWhisk 实例！

通过在 IBM Cloud Functions 上使用工作线程，我演示了如何通过并发使用多个内核来提高 CPU 密集型工作负载的性能。使用[一个素数生成的例子](https://gist.github.com/jthomas/154a039d52b97d5ed19d4ddac3ff9f43)，计算所有高达一千万的素数用单线程花费了大约 35 秒，用四线程花费了大约 8 秒。这意味着执行时间和成本减少了 75%！