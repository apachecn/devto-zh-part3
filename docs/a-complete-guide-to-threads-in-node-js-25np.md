# Node.js 中线程的完整指南

> 原文：<https://dev.to/bnevilleoneill/a-complete-guide-to-threads-in-node-js-25np>

[![](img/7a29ec601d8d149a848630c367230a24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7MgV7LSs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQTODd4nr6g7PNcLKqrlfCg.jpeg)

很多人想知道单线程 [Node.js](https://nodejs.org/en/) 怎么能和多线程后端竞争。因此，鉴于 Node 被认为是单线程的，那么多大公司选择 Node 作为他们的后端，这似乎是违反直觉的。要知道为什么，我们必须理解当我们说节点是单线程的时候，我们真正的意思是什么。

JavaScript 被创造出来只是为了在网络上做一些简单的事情，比如验证一个表单，或者创建一个彩虹色的鼠标轨迹。直到 2009 年，Node.js 的创始人瑞安·达尔(Ryan Dahl)才让开发者有可能使用这种语言编写后端代码。

后端语言通常支持多线程，有各种机制在线程和其他面向线程的特性之间同步值。要在 JavaScript 中增加对这些东西的支持，需要改变整个语言，这并不是 Dahl 的真正目标。为了让普通 JavaScript 支持多线程，他必须创建一个变通方法。让我们探索一下…

### node . js 真正的工作原理

Node.js 使用两种线程:一个由*事件循环*处理的主线程和几个在*工作池*中的辅助线程。

事件循环是一种机制，它接受回调(函数)并注册它们以便在将来的某个时间执行。它与正确的 JavaScript 代码在同一个线程中运行。当 JavaScript 操作阻塞线程时，事件循环也被阻塞。

Worker pool 是一个执行模型，它产生并处理单独的线程，然后这些线程同步执行任务并将结果返回给事件循环。然后，事件循环用所述结果执行所提供的回调。

简而言之，它负责异步 I/O 操作——主要是与系统磁盘和网络的交互。它主要由 fs (I/O 密集型)或 crypto (CPU 密集型)等模块使用。Worker pool 是在 [libuv](http://docs.libuv.org/en/v1.x/) 中实现的，这导致每当 Node 需要在 JavaScript 和 C++之间进行内部通信时都会有轻微的延迟，但这很难被注意到。

有了这两种机制，我们能够编写这样的代码:

```
fs.readFile(path.join(__dirname, './package.json'), (err, content) => {
 if (err) {
   return null;
 }
 console.log(content.toString());
}); 
```

前面提到的 fs 模块告诉 worker pool 使用它的一个线程来读取文件的内容，并在完成后通知事件循环。然后，事件循环获取提供的回调函数，并使用文件内容执行它。

上面是一个非阻塞代码的例子；因此，我们不必同步等待某事发生。我们告诉工作池读取文件，并使用结果调用提供的函数。由于工作池有自己的线程，因此在读取文件时，事件循环可以继续正常执行。

这一切都很好，直到需要同步执行一些复杂的操作:任何运行时间过长的函数都会阻塞线程。如果一个应用程序有许多这样的功能，它可能会显著降低服务器的吞吐量或者完全冻结它。在这种情况下，没有办法将工作委托给工人池。

需要复杂计算的领域-如人工智能、机器学习或大数据-无法真正有效地使用 Node.js，因为这些操作会阻塞主(也是唯一的)线程，使服务器无响应。在 Node.js v10.5.0 出现之前一直是这样，它增加了对多线程的支持。

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 简介:*工作者 _ 线程*

worker_threads 模块是一个包，它允许我们创建功能完整的多线程 Node.js 应用程序。

线程工作器是在一个单独的线程中产生的一段代码(通常取自一个文件)。

注意术语*线程工作者*、*工作者*和*线程*经常互换使用；它们指的都是同一个东西。

要开始使用线程工作器，我们必须导入 worker_threads 模块。让我们从创建一个函数来帮助我们产生这些线程工作器开始，然后我们将稍微讨论一下它们的属性。

```
type WorkerCallback = (err: any, result?: any) => any;
export function runWorker(path: string, cb: WorkerCallback, workerData: object | null = null) {
 const worker = new Worker(path, { workerData });

 worker.on('message', cb.bind(null, null));
 worker.on('error', cb);
 worker.on('exit', (exitCode) => {
   if (exitCode === 0) {
     return null;
   }
   return cb(new Error(`Worker has stopped with code ${exitCode}`));
 });
 return worker;
} 
```

要创建一个 worker，我们必须创建一个 Worker 类的实例。在第一个参数中，我们提供了包含工作者代码的文件的路径；在第二个示例中，我们提供了一个包含名为 workerData 的属性的对象。这是我们希望线程在开始运行时能够访问的数据。

请注意，无论您使用的是 JavaScript 本身还是将文件转换成 JavaScript 的东西(例如，TypeScript)，路径都应该始终指向带有这两者之一的文件。js 或者。mjs 扩展。

我还想指出为什么我们使用回调方法，而不是返回一个承诺，这个承诺将在消息事件触发时解决。这是因为工人可以分派许多消息事件，而不仅仅是一个。

正如您在上面的例子中所看到的，线程之间的通信是基于事件的，这意味着我们正在设置一旦 worker 发送给定事件就调用的侦听器。

以下是最常见的事件:

```
worker.on('error', (error) => {}); 
```

每当 worker 内部出现未捕获的异常时，就会发出 error 事件。然后，该工作线程被终止，该错误可作为所提供的回调中的第一个参数。

```
worker.on('exit', (exitCode) => {}); 
```

每当工作人员退出时，就会发出 exit。如果在 worker 内部调用了 process.exit()，将向回调提供 exitCode。如果 worker 是用 worker.terminate()终止的，代码将是 1。

```
worker.on('online', () => {}); 
```

每当工作人员停止解析 JavaScript 代码并开始执行时，就会发出 online。它不经常使用，但在特定情况下可以提供信息。

```
worker.on('message', (data) => {}); 
```

每当工作线程向父线程发送数据时，都会发出。

现在让我们看看数据是如何在线程间共享的。

### 线程间交换数据

为了将数据发送到另一个线程，我们使用 port.postMessage()方法。它有以下签名:

```
port.postMessage(data[, transferList]) 
```

端口对象可以是 parentPort，也可以是 MessagePort 的一个实例——稍后将详细介绍。

#### 数据自变量

第一个参数—这里称为数据—是复制到另一个线程的对象。它可以包含复制算法支持的任何内容。

数据由[结构化克隆算法](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)复制。根据 Mozilla:

> 它通过递归输入对象来构建克隆，同时维护以前访问过的引用的映射，以避免无限遍历循环。

该算法不复制函数、错误、属性描述符或原型链。还应该注意，以这种方式复制对象与用 JSON 不同，因为它可以包含循环引用和类型化数组，而 JSON 不能。

通过支持类型化数组的复制，该算法使线程间共享内存成为可能。

#### 线程间共享内存

人们可能会争辩说，像 cluster 或 child_process 这样的模块在很久以前就支持线程的使用。嗯，是也不是。

集群模块可以创建多个节点实例，其中一个主进程在它们之间路由传入的请求。集群化应用允许我们有效地增加服务器的吞吐量；然而，我们不能用集群模块产生一个单独的线程。

人们倾向于使用像 PM2 这样的工具来集群他们的应用，而不是在他们自己的代码中手动完成，但是如果你感兴趣，你可以[阅读我关于如何使用集群模块的帖子](https://medium.freecodecamp.org/how-to-add-socket-io-to-multi-threaded-node-js-df404b424276)。

child_process 模块可以生成任何可执行文件，不管它是否是 JavaScript。它非常相似，但是缺少 worker_threads 所具有的几个重要特性。

具体来说，线程工作器更加轻量级，并且与其父线程共享相同的进程 ID。它们还可以与它们的父线程共享内存，这使它们可以避免序列化大的数据负载，从而更有效地来回发送数据。

现在让我们看一个如何在线程间共享内存的例子。为了共享内存，必须将 ArrayBuffer 或 SharedArrayBuffer 的实例作为数据参数或在数据参数内发送给另一个线程。

这里有一个与其父线程共享内存的工作线程:

```
import { parentPort } from 'worker_threads';
parentPort.on('message', () => {
 const numberOfElements = 100;
 const sharedBuffer = new SharedArrayBuffer(Int32Array.BYTES_PER_ELEMENT * numberOfElements);
 const arr = new Int32Array(sharedBuffer);
 for (let i = 0; i < numberOfElements; i += 1) {
   arr[i] = Math.round(Math.random() * 30);
 }
 parentPort.postMessage({ arr });
}); 
```

首先，我们用包含 100 个 32 位整数所需的内存创建一个 SharedArrayBuffer。接下来，我们创建 Int32Array 的一个实例，它将使用缓冲区来保存其结构，然后我们只需用一些随机数填充数组，并将其发送给父线程。

在父线程中:

```
import path from 'path';

import { runWorker } from '../run-worker';

const worker = runWorker(path.join(__dirname, 'worker.js'), (err, { arr }) => {
 if (err) {
   return null;
 }
 arr[0] = 5;
});
worker.postMessage({}); 
```

通过将 arr[0]更改为 5，我们实际上在两个线程中都更改了它。

当然，通过共享内存，我们冒着在一个线程中改变一个值而在另一个线程中改变它的风险。但是我们也获得了一个非常好的特性:值不需要序列化就可以在另一个线程中使用，这大大提高了效率。只需记住正确管理对数据的引用，以便在使用完数据后对其进行垃圾收集。

共享一个整数数组很好，但是我们真正感兴趣的是共享对象——存储信息的默认方式。不幸的是，没有 SharedObjectBuffer 或类似的东西，但是我们可以[自己创建一个类似的结构](https://stackoverflow.com/questions/51053222/nodejs-worker-threads-shared-object-store)。

#### transfer list 参数

transferList 只能包含 ArrayBuffer 和 MessagePort。一旦它们被转移到另一个线程，就不能再在发送线程中使用；内存被移动到另一个线程，因此在发送线程中不可用。

目前，我们不能通过将网络套接字包含在 transferList 中来传输它们(这可以通过 child_process 模块来实现)。

#### 创建沟通渠道

线程之间的通信通过端口进行，端口是 MessagePort 类的实例，支持基于事件的通信。

有两种使用端口在线程间通信的方法。第一种是默认的，也是两者中比较容易的。在 worker 的代码中，我们从 worker_threads 模块导入一个名为 parentPort 的对象，并使用该对象的。postMessage()方法向父线程发送消息。

这里有一个例子:

```
import { parentPort } from 'worker_threads';
const data = {
// ...
};
parentPort.postMessage(data); 
```

parentPort 是 Node.js 在幕后为我们创建的 MessagePort 的一个实例，用于与父线程进行通信。这样，我们可以通过使用 parentPort 和 worker 对象在线程之间进行通信。

线程间通信的第二种方式是我们自己创建一个 MessageChannel 并发送给 worker。下面是我们如何创建一个新的 MessagePort 并与我们的工作人员共享它:

```
import path from 'path';
import { Worker, MessageChannel } from 'worker_threads';

const worker = new Worker(path.join(__dirname, 'worker.js'));

const { port1, port2 } = new MessageChannel();

port1.on('message', (message) => {
 console.log('message from worker:', message);
});
worker.postMessage({ port: port2 }, [port2]); 
```

在创建端口 1 和端口 2 之后，我们在端口 1 上设置事件监听器，并将端口 2 发送给 worker。我们必须将它包含在传送列表中，以便将其传送到工人端。

现在，在工人内部:

```
import { parentPort, MessagePort } from 'worker_threads';
parentPort.on('message', (data) => {
 const { port }: { port: MessagePort } = data;
 port.postMessage('heres your message!');
}); 
```

这样，我们使用父线程发送的端口。

使用 parentPort 不一定是错误的方法，但是最好用 MessageChannel 的一个实例创建一个新的 MessagePort，然后与衍生的 worker 共享它(阅读:关注点分离)。

注意，在下面的例子中，为了简单起见，我使用了 parentPort。

### 工人的两种使用方式

我们可以通过两种方式使用工人。第一个是产生一个工作线程，执行它的代码，并将结果发送给父线程。使用这种方法，每次出现新任务时，我们都必须重新创建一个工人。

第二种方法是生成一个 worker 并为消息事件设置监听器。每次触发消息时，它都会执行工作，并将结果发送回父线程，从而使工作线程保持活动状态以备后用。

Node.js 文档推荐第二种方法，因为实际创建一个线程工作器需要花费很多精力，这需要创建一个虚拟机并解析和执行代码。这种方法也比不断产卵的工人效率高得多。

这种方法被称为工作者池，因为我们创建了一个工作者池，让他们等待，在需要时分派消息事件来完成工作。

下面是一个文件的例子，它包含一个被生成、执行然后关闭的 worker:

```
import { parentPort } from 'worker_threads';

const collection = [];

for (let i = 0; i < 10; i += 1) {
 collection[i] = i;
}
parentPort.postMessage(collection); 
```

将集合发送到父线程后，它就退出了。

这里有一个例子，一个工人可以在接到任务之前等待很长时间:

```
import { parentPort } from 'worker_threads';

parentPort.on('message', (data: any) => {
 const result = doSomething(data);

 parentPort.postMessage(result);
}); 
```

### worker _ threads 模块中可用的有用属性

worker_threads 模块中有一些可用的属性:

#### 是一条主线

当不在工作线程内操作时，属性为 true。如果您觉得有必要，可以在 worker 文件的开头包含一个简单的 If 语句，以确保它只作为 worker 运行。

```
import { isMainThread } from 'worker_threads';
if (isMainThread) {
 throw new Error('Its not a worker');
} 
```

#### 工人数据

由生成线程包含在工作线程的构造函数中的数据。

```
const worker = new Worker(path, { workerData }); 
```

在工作线程中:

```
import { workerData } from 'worker_threads';
console.log(workerData.property); 
```

#### parentPort

前面提到的 MessagePort 实例用于与父线程通信。

#### threadId

分配给员工的唯一标识符。

现在我们知道了技术细节，让我们实现一些东西，并在实践中检验我们的知识。

### 实现 setTimeout

setTimeout 是一个无限循环，顾名思义就是让 app 超时。实际上，它在每次迭代中检查开始日期和给定的毫秒数之和是否小于实际日期。

```
import { parentPort, workerData } from 'worker_threads';
const time = Date.now();
while (true) {
 if (time + workerData.time <= Date.now()) {
   parentPort.postMessage({});
   break;
 }
} 
```

这个特定的实现产生一个线程，执行它的代码，然后在完成后退出。

让我们尝试实现将利用这个工人的代码。首先，让我们创建一个状态，在这个状态中，我们将跟踪产生的工人:

```
const timeoutState: { [key: string]: Worker } = {}; 
```

现在，这个函数负责创建工人并将他们保存到状态:

```
export function setTimeout(callback: (err: any) => any, time: number) {
 const id = uuidv4();
 const worker = runWorker(
   path.join(__dirname, './timeout-worker.js'),
   (err) => {
     if (!timeoutState[id]) {
       return null;
     }
     timeoutState[id] = null;
     if (err) {
       return callback(err);
     }
     callback(null);
   },
   {
     time,
   },
 );
 timeoutState[id] = worker;
 return id;
} 
```

首先，我们使用 UUID 包为我们的工人创建一个惟一的标识符，然后我们使用先前定义的帮助器函数 runWorker 来获取工人。我们还传递给 worker 一个回调函数，一旦 worker 发送了一些数据，这个函数就会被触发。最后，我们在状态中保存工人并返回 id。

在回调函数中，我们必须检查 worker 是否仍然存在于状态中，因为存在 cancelTimeout()的可能性，这将删除它。如果它确实存在，我们将其从状态中移除，并调用传递给 setTimeout 函数的回调。

cancelTimeout 函数使用。terminate()方法来强制工作线程退出，并将该工作线程从状态:

```
export function cancelTimeout(id: string) {
 if (timeoutState[id]) {
   timeoutState[id].terminate();
   timeoutState[id] = undefined;
   return true;
 }
 return false;
} 
```

如果你感兴趣，我也实现了 setInterval [这里](https://github.com/maciejcieslar/threads-nodejs/blob/master/src/timeout/timeout.ts#L64)，但是由于它与线程无关(我们重用 setTimeout 的代码)，我决定不在这里包括解释。

我创建了一个小测试代码，目的是检查这种方法与原生方法有多大不同。你可以在这里查看代码[。这些是结果:](https://github.com/maciejcieslar/threads-nodejs/blob/master/src/timeout/index.ts#L13) 

```
native setTimeout { ms: 7004, averageCPUCost: 0.1416 }
worker setTimeout { ms: 7046, averageCPUCost: 0.308 } 
```

我们可以看到，由于创建了 worker，setTimeout 有一点延迟，大约 40 毫秒。平均 CPU 成本也稍微高一点，但没有高到无法承受的程度(CPU 成本是整个进程持续时间内 CPU 使用的平均值)。

如果我们可以重用工人，我们将降低延迟和 CPU 使用率，这就是为什么我们现在要看看如何实现我们自己的工人池。

### 实现一个工人池

如上所述，工作线程池是给定数量的先前创建的工作线程，这些工作线程坐着监听消息事件。一旦触发了消息事件，它们就开始工作并发回结果。

为了更好地说明我们将要做的事情，下面是我们如何创建一个由八个线程工作者组成的工作者池:

```
const pool = new WorkerPool(path.join(__dirname, './test-worker.js'), 8); 
```

如果你熟悉[限制并发操作](https://medium.freecodecamp.org/how-to-limit-concurrent-operations-in-javascript-b57d7b80d573)，那么你会看到这里的逻辑几乎是一样的，只是不同的用例。

如上面的代码片段所示，我们向 WorkerPool 的构造函数传递 worker 的路径和要生成的 worker 数量。

```
export class WorkerPool<T, N> {
 private queue: QueueItem<T, N>[] = [];
 private workersById: { [key: number]: Worker } = {};
 private activeWorkersById: { [key: number]: boolean } = {};
 public constructor(public workerPath: string, public numberOfThreads: number) {
   this.init();
 }
} 
```

这里，我们有像 workersById 和 activeWorkersById 这样的附加属性，在这些属性中，我们可以分别保存现有的 worker 和当前正在运行的 worker 的 Id。还有队列，我们可以用下面的结构保存对象:

```
type QueueCallback<N> = (err: any, result?: N) => void;
interface QueueItem<T, N> {
 callback: QueueCallback<N>;
 getData: () => T;
} 
```

callback 只是默认的节点回调，error 作为第一个参数，可能的结果作为第二个参数。getData 是传递给工作池的。run()方法(如下所述)，一旦开始处理项目，就会调用该方法。getData 函数返回的数据将被传递给工作线程。

在里面。init()方法，我们创建工人并把他们保存在状态:

```
private init() {
  if (this.numberOfThreads < 1) {
    return null;
  }
  for (let i = 0; i < this.numberOfThreads; i += 1) {
    const worker = new Worker(this.workerPath);
    this.workersById[i] = worker;
    this.activeWorkersById[i] = false;
  }
} 
```

为了避免无限循环，我们首先确保线程数大于 1。然后，我们创建有效的工作线程数量，并根据它们在 workersById 状态中的索引来保存它们。我们保存关于它们当前是否在 activeWorkersById 状态下运行的信息，默认情况下，首先总是 false。

现在，我们必须实施上述措施。run()方法来设置一个任务，以便在有工作人员时运行。

```
public run(getData: () => T) {
  return new Promise<N>((resolve, reject) => {
    const availableWorkerId = this.getInactiveWorkerId();
    const queueItem: QueueItem<T, N> = {
      getData,
      callback: (error, result) => {
        if (error) {
          return reject(error);
        }
return resolve(result);
      },
    };
   if (availableWorkerId === -1) {
      this.queue.push(queueItem);
      return null;
    }
    this.runWorker(availableWorkerId, queueItem);
  });
} 
```

在传递给 promise 的函数中，我们首先通过调用。getInactiveWorkerId():

```
private getInactiveWorkerId(): number {
  for (let i = 0; i < this.numberOfThreads; i += 1) {
    if (!this.activeWorkersById[i]) {
      return i;
    }
  }
  return -1;
} 
```

接下来，我们创建一个 queueItem，在其中保存传递给。run()方法以及回调。在回调中，我们根据工人是否向回调传递了错误来解决或拒绝承诺。

如果 availableWorkerId 为-1，那么就没有可用的 worker，我们将 queueItem 添加到队列中。如果有可用的工人，我们调用。runWorker()方法来执行 Worker。

在。runWorker()方法，我们必须在 activeWorkersById 内部设置当前正在使用的 Worker 状态；为消息和错误事件设置事件侦听器(并在事后清理它们)；最后，将数据发送给员工。

```
private async runWorker(workerId: number, queueItem: QueueItem<T, N>) {
 const worker = this.workersById[workerId];
 this.activeWorkersById[workerId] = true;
 const messageCallback = (result: N) => {
   queueItem.callback(null, result);
   cleanUp();
 };
 const errorCallback = (error: any) => {
   queueItem.callback(error);
   cleanUp();
 };
 const cleanUp = () => {
   worker.removeAllListeners('message');
   worker.removeAllListeners('error');
   this.activeWorkersById[workerId] = false;
   if (!this.queue.length) {
     return null;
   }
   this.runWorker(workerId, this.queue.shift());
 };
 worker.once('message', messageCallback);
 worker.once('error', errorCallback);
 worker.postMessage(await queueItem.getData());
} 
```

首先，通过使用传递的 workerId，我们从 workersById 状态获取 worker 引用。然后，在 activeWorkersById 中，我们将[workerId]属性设置为 true，这样我们就知道在 worker 忙碌时不要运行任何其他内容。

接下来，我们创建 messageCallback 和 errorCallback，分别在 message 和 error 事件上调用，然后注册所述函数来监听事件并将数据发送给 worker。

在回调函数中，我们调用 queueItem 的回调函数，然后调用 cleanUp 函数。在 cleanUp 函数中，我们确保事件侦听器被删除，因为我们多次重用同一个 worker。如果我们不删除侦听器，我们就会有内存泄漏；本质上，我们会慢慢耗尽内存。

在 activeWorkersById 状态中，我们将[workerId]属性设置为 false，并检查队列是否为空。如果不是，我们从队列中移除第一个项目，并使用不同的 queueItem 再次调用工作线程。

让我们创建一个在接收到消息事件:
中的数据后进行一些计算的 worker

```
import { isMainThread, parentPort } from 'worker_threads';
if (isMainThread) {
 throw new Error('Its not a worker');
}
const doCalcs = (data: any) => {
 const collection = [];
 for (let i = 0; i < 1000000; i += 1) {
   collection[i] = Math.round(Math.random() * 100000);
 }
 return collection.sort((a, b) => {
   if (a > b) {
     return 1;
   }
   return -1;
 });
};
parentPort.on('message', (data: any) => {
 const result = doCalcs(data);
 parentPort.postMessage(result);
}); 
```

这个工人创建了一个由一百万个随机数组成的数组，然后对它们进行排序。只要花些时间完成，发生什么并不重要。

下面是一个简单使用工人池的例子:

```
const pool = new WorkerPool<{ i: number }, number>(path.join(__dirname, './test-worker.js'), 8);
const items = [...new Array(100)].fill(null);
Promise.all(
 items.map(async (_, i) => {
   await pool.run(() => ({ i }));
   console.log('finished', i);
 }),
).then(() => {
 console.log('finished all');
}); 
```

我们首先创建一个由八名员工组成的人才库。然后，我们创建一个包含 100 个元素的数组，对于每个元素，我们在 worker 池中运行一个任务。首先，立即执行八个任务，其余的放在队列中，逐步执行。通过使用工人池，我们不必每次都创建工人，这大大提高了效率。

### 结论

worker_threads 为我们的应用程序添加多线程支持提供了一个相当简单的方法。通过将繁重的 CPU 计算委派给其他线程，我们可以显著提高服务器的吞吐量。有了官方线程的支持，我们可以期待更多来自人工智能、机器学习和大数据等领域的开发人员和工程师开始使用 Node.js。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[node . js 中线程的完整指南](https://blog.logrocket.com/a-complete-guide-to-threads-in-node-js-4fa3898fe74f/)首先出现在[的 LogRocket 博客](https://blog.logrocket.com)上。