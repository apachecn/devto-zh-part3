# 异步 JavaScript:历史、模式和陷阱

> 原文：<https://dev.to/hugo__df/async-javascript-history-patterns-and-gotchas-53d6>

看看 JavaScript 中异步操作的历史、模式和陷阱。

我们将讨论回调、承诺和异步/等待的利弊。提出一些要记住的陷阱，并介绍你将如何处理某些情况。

在[github . com/Hugo df/async-JS-presentation/tree/master/workshop](https://github.com/HugoDF/async-js-presentation/tree/master/workshop)上的现场编码/研讨会部分涉及节点和客户端 JS 情况。

这是在 2019 年 1 月伦敦 Codebar 月刊上的一次演讲，请参见幻灯片:

[通过 GitHub repo](https://speakerdeck.com/hugodf/async-javascript-history-patterns-and-gotchas) 在 SpeakerDeck 或[上查看原始幻灯片。](https://github.com/HugoDF/async-js-presentation)

**目录🐳**:

*   [JavaScript 中的异步](#asynchronicity-in-javascript)
    *   web 应用程序中的异步是什么？
    *   [为什么非阻塞 I/O？](#why-non-blocking-io)
*   [节点风格的回调](#node-style-callbacks)
    *   [节点风格的回调:问题](#node-style-callbacks-problems)
    *   [1。回调*地狱*T3】](#1-callback-hell)
    *   [2。隐藏变量](#2-shadowing-variables)
    *   [3。重复错误处理](#3-duplicated-error-handling)
    *   [4。吞咽错误](#4-swallowed-errors)
    *   [回调问题](#callback-problems)
*   [兑现承诺](#bring-on-the-promise)
    *   [优点](#pros)
    *   [缺点](#cons)
    *   [承诺抓到你](#promise-gotchas)
    *   [筑巢很诱人](#nesting-them-is-tempting)
    *   [onRejected 回调](#onrejected-callback)
*   [异步/等待](#asyncawait)
    *   [示例:循环顺序调用](#example-loop-through-sequential-calls)
    *   [示例:在呼叫之间共享数据](#example-share-data-between-calls)
    *   [示例:错误处理](#example-error-handling)
    *   [异步/等待的缺点](#cons-of-asyncawait)
*   [抓到你了](#gotchas)
    *   [产生错误](#creating-an-error)
    *   当你忘记等待时会发生什么？
    *   [许诺热切地评价✨](#promises-evaluate-eagerly-)
    *   [测试陷阱📙](#testing-gotchas-)
*   [图案](#patterns)
    *   [并行兑现承诺🏃](#running-promises-in-parallel-)
    *   [延迟履行承诺](#delay-execution-of-a-promise)
    *   [分离同步和异步操作](#separate-synchronous-and-asynchronous-operations)
    *   [连续运行承诺](#running-promises-sequentially)
    *   [在顺序异步调用中传递数据](#passing-data-in-sequential-async-calls)
    *   [错误处理](#error-handling)
*   [车间示例](#workshop-examples)
    *   [“回调”基于承诺的 API](#callbackify-ing-a-promise-based-api)
    *   [使用回调并行获取数据:痛苦](#getting-data-in-parallel-using-callbacks-the-pain)
    *   [“承诺”——基于回调的 API](#promisify-ing-a-callback-based-api)
    *   [为什么我们不混合异步和同步操作](#why-we-dont-mix-async-and-sync-operations)
*   [延伸阅读](#further-reading)

## JavaScript 中的异步性

原语:-回调-承诺-(可观察到的)- `async/await`

### web 应用中的异步是什么？

大部分事情:1。任何网络调用(HTTP、数据库)2。定时器(`setTimeout`，`setInterval` )3。文件系统访问…任何可以卸载的东西

在 JavaScript 中，这些操作是非阻塞的。

Python 中的 HTTP 请求:

```
data = request(myUrl)
print(data) 
```

JavaScript 中的 HTTP 请求:

```
request(myUrl, (err, data) => {
  console.log(data);
}); 
```

### 为什么非阻塞 I/O？

JavaScript 被认为是一种 UI 编程语言。例如，在 UI 中，你不希望在等待服务器响应时冻结 UI 交互。

非阻塞 I/O 意味着等待不会耗费您的计算周期。

非阻塞 I/O 是如何实现的(用 JavaScript):-传递一个“回调”函数-异步操作的结果调用它

## 节点式回调

```
myAsyncFn((err, data) => {
  if (err) dealWithIt(err);
  doSomethingWith(data);
}) 
```

回拨是:

*   “只是”一个功能
*   在例子中，通常是匿名函数(直接传递`function () {}`)
*   根据一些样式指南，应该是箭头功能(`() => {}`)
*   异步操作时调用

节点样式的回调是:

*   以任何错误作为第一个参数调用，如果没有错误，则传递`null`
*   使用任意数量的“输出”数据作为其他参数进行调用

即。`(err, data) => { /* more logic */ }`

### 节点式回调:问题

#### 1。回调*地狱*

```
myAsyncFn((err, data) => {
  if (err) handle(err)
  myOtherAsyncFn(data, (err, secondData) => {
    fun(data, secondData, (err) => {
      if (err) handle(err)
    })
    fn(data, secondData, (err) => {
      if (err) handle(err)
    })
  })
}) 
```

对于每个异步操作:-额外的缩进级别-异步输出的许多名称:`data`，`secondData`

#### 2。隐藏变量

```
myAsyncFn((err, data) => {
  if (err) handle(err)
  myOtherAsyncFn(data, (err, secondData) => {
    fun(data, secondData, (err) => {
      if (err) handle(err)
    })
    fn(data, secondData, (err) => {
      if (err) handle(err)
    })
  })
}) 
```

*   `err`(在`myAsyncFn`回调)！== `err`(在`myOtherAsyncFn`回调中)尽管有相同的 nam

#### 3。重复错误处理

*   每次操作调用 1 次`handle(err)`

```
myAsyncFn((err, data) => {
  if (err) handle(err)
  myOtherAsyncFn(data, (err, secondData) => {
    fun(data, secondData, (err) => {
      if (err) handle(err)
    })
    fn(data, secondData, (err) => {
      if (err) handle(err)
    })
  })
}) 
```

#### 4。隐藏的错误

理想的失败:-失败得早-失败得快-失败得响

发现未处理的错误:

```
myAsyncFn((err, data) => {
  if (err) handle(err)
  myOtherAsyncFn(data, (err, secondData) => {
    fun(data, secondData, (err) => {
      if (err) handle(err)
    })
    fn(data, secondData, (err) => {
      if (err) handle(err)
    })
  })
}) 
```

**无声错误**是注释所在的地方。

```
myAsyncFn((err, data) => {
  if (err) handle(err)
  myOtherAsyncFn(data, (err, secondData) => {
    // Missing error handling!
    fun(data, secondData, (err) => {
      if (err) handle(err)
    })
    fn(data, secondData, (err) => {
      if (err) handle(err)
    })
  })
}) 
```

那个`err`没有得到处理。Linters 会发现这一点(我希望)，抱怨说`err`被定义了但是没有被使用。那是生活在边缘的一点点。

### 回调问题

回调的问题归结为以下几点。

回调地狱有许多缩进和变量名。

**隐藏的变量**带来的所有问题。

**重复的错误处理**使得**很容易吞下错误**。

## 兑现承诺

```
myAsyncFn()
  .then((data) => Promise.all([
    data,
    myOtherAsyncFn(data),
  ]))
  .then(([data, secondData]) => Promise.all([
    fun(data, secondData),
    fn(data, secondData),
  ]))
  .then(/* do anything else */)
  .catch((err) => handle(err)); 
```

### 优点

承诺是**可链接的**，你可以从`.then`返回一个承诺，跟踪另一个`.then`并继续下去，没有疯狂的订单。

您可以使用添加到承诺链末尾的`.catch`来定义一个**单一错误处理程序**。

每个异步步骤一个小函数(在`.then`内部)使得分解长的异步流变得更加容易。

### 弊

你定义了很多严格限定范围的函数，从一个调用到下一个调用传递数据是非常冗长的

```
.then((data) => Promise.all([
  data,
  myOtherAsyncFn(data),
]) 
```

### 许诺抓到你

#### 筑巢引凤很诱人

```
myAsyncFn()
  .then((data) =>
    myOtherAsyncFn(data)
      .then(
        ([data, secondData]) =>
          Promise.all([
            fun(data, secondData),
            fn(data, secondData),
          ])
      )
  )
  .catch((err) => handle(err)) 
```

解决方案:避开☠️的末日金字塔

```
myAsyncFn()
  .then((data) => Promise.all([
    data,
    myOtherAsyncFn(data),
  ]))
  .then(([data, secondData]) => Promise.all([
    fun(data, secondData),
    fn(data, secondData),
  ]))
  .then(/* do anything else */)
  .catch((err) => handle(err)) 
```

承诺“扁平化”，您可以从一个`then`返回一个承诺，并继续添加期望解析值的`.then`。

### onRejected 回调

`.then`取两个参数，`onResolved`和`onRejected`，所以下面的工作:

```
myAsyncFn()
  .then(
    (data) => myOtherAsyncFn(data),
    (err) => handle(err)
  ); 
```

但是我们又回到了像回调中那样进行每个操作的错误处理(潜在的吞咽错误等)。)

解决方法:避开它，偏向`.catch`

```
myAsyncFn()
  .then(
    (data) => myOtherAsyncFn(data)
  )
  .catch((err) => handle(err)); 
```

*除非你特别需要*，例如，当你使用`redux-thunk`并进行 HTTP 调用时，你还会从 React`.catch`呈现错误。

在这种情况下，最好使用`onRejected`。

## 异步/等待

```
(async () => {
  try {
    const data = await myAsyncFn();
    const secondData = await myOtherAsyncFn(data);
    const final = await Promise.all([
      fun(data, secondData),
      fn(data, secondData),
    ]);
    /* do anything else */
  } catch (err) {
    handle(err);
  }
})(); 
```

给定一个承诺(或任何具有`.then`函数的对象)，`await`获取传递给`.then`中回调的值。

`await`只能在`async`的函数中使用。顶级(异步函数之外)await 即将到来，但目前你会得到一个语法错误。

```
(async () => {
  console.log('Immediately invoked function expressions (IIFEs) are cool again')
  const res = await fetch('https://jsonplaceholder.typicode.com/todos/2')
  const data = await res.json()
  console.log(data)
})()

// SyntaxError: await is only valid in async function
const res = await fetch(
  'https://jsonplaceholder.typicode.com/todos/2'
) 
```

功能是“只是”承诺。这意味着你可以调用一个`async`函数并给它加上一个`.then`。

```
const arrow = async () => { return 1 }
const implicitReturnArrow = async () => 1
const anonymous = async function () { return 1 }
async function expression () { return 1 }

console.log(arrow()); // Promise { 1 }
console.log(implicitReturnArrow()); // Promise { 1 }
console.log(anonymous()); // Promise { 1 }
console.log(expression()); // Promise { 1 } 
```

### 示例:循环顺序调用

带异步/等待:

```
async function fetchSequentially(urls) {
  for (const url of urls) {
    const res = await fetch(url);
    const text = await res.text();
    console.log(text.slice(0, 100));
  }
} 
```

有了承诺:

```
function fetchSequentially(urls) {
  const [url, ...rest] = urls
  fetch(url)
    .then(res => res.text())
    .then(text => console.log(text.slice(0, 100)))
    .then(fetchSequentially(rest));
} 
```

### 举例:通话间共享数据

`const myVariable = await fetchThing()` - >轻松

```
async function run() {
  const data = await myAsyncFn();
  const secondData = await myOtherAsyncFn(data);
  const final = await Promise.all([
    fun(data, secondData),
    fn(data, secondData),
  ]);

  return final
} 
```

我们没有完整的承诺流程:

```
.then(() => Promise.all([dataToPass, promiseThing]))
.then(([data, promiseOutput]) => { }) 
```

### 举例:错误处理

在下面的例子中，`try/catch`获取任何错误并记录下来。

函数的调用者不知道任何失败。

```
async function withErrorHandling(url) {
  try {
    const res = await fetch(url);
    const data = await res.json();
    return data
  } catch(e) {
    console.log(e.stack)
  }
}

withErrorHandling(
  'https://jsonplaceholer.typicode.com/todos/2'
  // The domain should be jsonplaceholder.typicode.com
).then(() => { /* but we'll end up here */ }) 
```

### 异步/等待的缺点

浏览器支持仅在最新/现代浏览器中有效。

Polyfills (async-to-gen，regenerator runtime)很大，所以如果你只是将 async/await 用于语法糖，坚持承诺是个好主意。

虽然 Node 8+本身支持它，但没有插件，没有编译，没有聚合填充，所以异步/等待就在那里。

热衷于函数式编程的人会说这会导致更“命令式”的编程风格，我不喜欢缩进，所以我不听这种说法。

## 明白了

### 创建错误

`async`函数内部的`throw` -ing 和`return Promise.reject`的工作是一样的

`.reject`和`throw` `Error`对象拜托，你永远不知道哪个库会做`instanceof Error`检查。

```
async function asyncThrow() {
  throw new Error('asyncThrow');
}
function rejects() {
  return Promise.reject(new Error('rejects'))
}
async function swallowError(fn) {
  try { await asyncThrow() }
  catch (e) { console.log(e.message, e. __proto__ ) }
  try { await rejects() }
  catch (e) { console.log(e.message, e. __proto__ ) }
}
swallowError() // asyncThrow Error {} rejects Error {} 
```

### 当你忘记等待时会发生什么？

价值是未定义的，承诺是一个几乎没有属性的对象。

你会经常看到:`TypeError: x.fn is not a function`。

```
async function forgotToWait() {
  try {
    const res = fetch('https://jsonplaceholer.typicode.com/todos/2')
    const text = res.text()
  } catch (e) {
    console.log(e);
  }
}

forgotToWait()
// TypeError: res.text is not a function 
```

Promise/async 函数(只是一个 Promise)的`console.log`输出是:`Promise { <pending> }`。

当您开始调试您的应用程序和一个应该包含这样一个值日志的变量时，您可能在某个地方忘记了一个`await`。

```
async function forgotToWait() {
  const res = fetch('https://jsonplaceholer.typicode.com/todos/2')
  console.log(res)
}

forgotToWait()
// Promise { <pending> } 
```

### 许诺热切地评价✨

承诺不会等待任何事情去执行，当你创造了它，它就会运行:

```
new Promise((resolve, reject) => {
  console.log('eeeeager');
  resolve();
}) 
```

上面的代码会立即打印出‘eee eager’，提示:不要创建你不想运行的承诺。

### 测试陷阱📙

Jest 支持承诺作为测试输出(因此也支持`async`函数):

```
const runCodeUnderTest = async () => {
  throw new Error();
};

test('it should pass', async () => {
  doSomeSetup();

  await runCodeUnderTest();
  // the following never gets run
  doSomeCleanup();
}) 
```

如果测试失败，`doSomeCleanup`函数不会被调用，因此可能会出现级联失败。

在“之前/之后”钩子中进行清理，异步测试体崩溃并且不清理。

```
describe('feature', () => {
  beforeEach(() => doSomeSetup())
  afterEach(() => doSomeCleanup())
  test('it should pass', async () => {
    await runCodeUnderTest();
  })
}) 
```

## 图案

其中很多都是为了避免我们在“陷阱”一节中看到的陷阱。

### 并行许诺🏃

使用期望一个承诺数组的`Promise.all`，等待直到它们全部解析(完成)并调用带有解析值数组的`.then`处理程序。

```
function fetchParallel(urls) {
  return Promise.all(
    urls.map(
      (url) =>
      fetch(url).then(res => res.json())
    )
  );
} 
```

在一个`async`函数上使用`Promise.all` + `map`，一个异步函数“只是一个承诺”。

适用于日志记录或当您有重要的/业务逻辑时

```
function fetchParallel(urls) {
  return Promise.all(
    urls.map(async (url) => {
      const res = await fetch(url);
      const data = await res.json();
      return data;
    })
  );
} 
```

### 延迟履行诺言

承诺是热切的，他们只是想跑！要延迟它们，将它们包装在一个返回承诺的函数中。

```
function getX(url) {
  return fetch(url)
}

// or

const delay = url => fetch(url) 
```

没有承诺，没有急切的执行。喜欢幻想的人会把上面的称为“thunk”，这是一种延迟执行/计算的模式。

### 分开同步和异步操作

许多依赖异步读写操作的 web 应用程序的流程如下。

获取数据，进行异步操作。使用内存中的数据运行同步操作。用异步调用写回数据。

```
const fs = require('fs').promises

const fetchFile = () =>
  fs.readFile('path', 'utf-8');
const replaceAllThings = (text) =>
  text.replace(/a/g, 'b');
const writeFile = (text) =>
  fs.writeFile('path', text, 'utf-8');

(async () => {
  const text = await fetchFile();
  const newText = replaceAllThings(text);
  await writeFile(newText);
})(); 
```

很多内置函数不会等待承诺来解决。如果您混合了字符串操作/替换和承诺，您将会在代码注入承诺对象而不是解析值的所有地方都以`[object Promise]`结束。

### 按顺序运行承诺

使用递归+ rest/spread 和太多簿记方式…

```
function fetchSequentially(urls, data = []) {
  if (urls.length === 0) return data
  const [url, ...rest] = urls
  return fetch(url)
    .then(res => res.text())
    .then(text =>
      fetchSequentially(
        rest,
        [...data, text]
      ));
} 
```

使用`await` + a 循环，少记账，更易阅读。

```
async function fetchSequentially(urls) {
  const data = []
  for (const url of urls) {
    const res = await fetch(url);
    const text = await res.text();
    data.push(text)
  }
  return data
} 
```

记住，只有在第 n 次调用依赖于前一次调用的输出时，才进行后续调用。否则，您也许可以并行运行整个过程。

### 在顺序异步调用中传递数据

在下一个调用中返回数组+析构，在承诺链中非常冗长:

```
async function findLinks() { /* some implementation */ }

function crawl(url, parentText) {
  console.log('crawling links in: ', parentText);
  return fetch(url)
    .then(res => res.text())
    .then(text => Promise.all([
      findLinks(text),
      text
    ]))
    .then(([links, text]) => Promise.all(
      links.map(link => crawl(link, text))
    ));
} 
```

在闭包中使用`await` +数据:

```
async function findLinks() { /* someimplementation */ }

async function crawl(url, parentText) {
  console.log('crawling links in: ', parentText);
  const res = await fetch(url);
  const text = await res.text();
  const links = await findLinks(text);
  return crawl(links, text);
} 
```

### 错误处理

使用 try/catch，或`.catch`，try/catch 意味着您也将`catch`发生同步错误。

```
function withCatch() {
  return fetch('borked_url')
    .then(res => res.text())
    .catch(err => console.log(err))
}

async function withBlock() {
  try {
    const res = await fetch('borked_url');
    const text = await res.text();
  } catch (err) {
    console.log(err)
  }
} 
```

## 车间实例

位于[github . com/Hugo df/async-js-presentation/tree/master/workshop](https://github.com/HugoDF/async-js-presentation/tree/master/workshop)的示例代码

### “回调”——使用基于承诺的 API

我们将使用`fetch`(参见 [MDN 关于获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)的文章)，这是一个浏览器 API，它公开了一个基于 Promise 的 API 来进行 HTTP 调用。

我们将编写一个`get(url, callback)`函数，它接受一个 URL，从中获取 JSON，并用它(或错误)调用回调。

我们将这样使用它:

```
get('https://jsonplaceholder.typicode.com/todos', (err, data) => {
  console.log(data)
}) 
```

让我们用正确的参数定义一个`get`函数，调用获取 URL 并获取数据:

```
// only needed in Node
const fetch = require('node-fetch')

function get(url, callback) {
  fetch(url)
    .then((res) => res.json())
    .then((data) => { /* we have the data now */})
} 
```

一旦我们有了数据，我们可以用`null, data` :
调用`callback`

```
// only needed in Node
const fetch = require('node-fetch')

function get(url, callback) {
  fetch(url)
    .then((res) => res.json())
    .then((data) => callback(null, data))
} 
```

并添加错误处理步骤，`.catch((err) => callback(err))` :

```
// only needed in Node
const fetch = require('node-fetch')

function get(url, callback) {
  fetch(url)
    .then((res) => res.json())
    .then((data) => callback(null, data))
    .catch((err) => callback(err))
} 
```

就这样，我们编写了一个包装器，它使用回调 API 向基于 Promise 的客户端发出 HTTP 请求。

### 使用回调并行获取数据:痛苦

接下来，我们将编写一个函数，使用我们在上一节中定义的`get`函数从 jsonplaceholder API 通过 id 获取 todos。

它的用法如下所示(获取 ids 1，2，3，10，22):

```
getTodosCallback([1, 2, 3, 10, 22], (err, data) => {
  if (err) return console.log(err)
  console.log(data)
}) 
```

让我们定义函数，我们获取 id 数组，用它的 URL (baseUrl + id)调用`get`。

在对`get`的回调中，我们将检查错误。

此外，如果已经获取了所有 id 的数据，我们将使用所有数据调用回调。

这需要大量的簿记工作，而且它甚至不一定以正确的顺序返回数据。

```
const baseUrl = 'https://jsonplaceholder.typicode.com/todos'

function getTodosCallback(ids, callback) {
  const output = []
  const expectedLength = ids.length

  ids.forEach(id => {
    get(`${baseUrl}/${id}`, (err, data) => {
      if (err) callback(err)

      output.push(data)

      if (output.length === expectedLength) {
        callback(null, output)
      }
    })
  })
} 
```

下面是用直接`fetch` :
实现的相同功能

```
function getTodosPromise(ids) {
  return Promise.all(
    ids.map(async (id) => {
      const res = await fetch(`${baseUrl}/${id}`);
      const data = await res.json();
      return data;
    })
  )
} 
```

更短，更密集，并按顺序返回东西。

### “承诺”——基于回调的 API

历史上，Node 的 API，尤其是`fs`已经使用了回调 API。

让我们用一个承诺代替`readFile(filePath, options, (err, data) => {})`来读一个文件。

我们希望能够像这样使用它:

```
readFile('./01-callbackify-fetch.js', 'utf8')
  .then(console.log) 
```

`Promise`构造函数接受一个有两个参数的函数，resolve 和 reject。它们都是函数，我们希望`resolve()`有成功的值，而`reject()`有错误。

所以我们以下面的话结束:

```
const fs = require('fs')

function readFile(path, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(path, encoding, (err, text) => {
      if (err) return reject(err)
      resolve(text)
    })
  })
} 
```

这就是全部了。

### 为什么我们不混合异步和同步操作

让我们定义一个抽象问题:我在一个文件夹中有一些关于浏览器信息的 JSON 文件。

给定一段包含浏览器名称的文本，我想从文件夹中的文件注入统计信息。

让我们做一个简单的实现，我们有一个`loadBrowserData`异步函数读取文件并`JSON.parse`保存它。

我们有一个`badIdea`异步函数，它遍历浏览器并调用`text.replace()`，将浏览器名称作为第一个参数，另一个异步函数获取数据并将其格式化作为第二个参数。

`String.replace`支持回调作为第二个参数，但它不支持它，它只需要一个同步函数，这意味着下面的代码:

```
const fs = require('fs').promises
const path = require('path')

const browsers = ['chrome', 'edge', 'firefox', 'safari']

async function loadBrowserData(name) {
  const data = await fs.readFile(path.resolve(__dirname, './04-data', `${name}.json`), 'utf8');
  return JSON.parse(data)
}

async function badIdea(text) {
  let newText = text
  browsers.forEach((browser) => {
    newText = newText.replace(browser, async (match) => {
      const {
        builtBy,
        latestVersion,
        lastYearUsage
      } = await loadBrowserData(browser);
      return `${browser} (${builtBy}, latest version: ${latestVersion}, usage: ${lastYearUsage})`
    })
  })
  return newText
}

const myText = `
We love chrome and firefox.

Despite their low usage, we also <3 safari and edge.
`;

(async () => {
  console.log(await badIdea(myText));
})() 
```

注销:

```
We love [object Promise] and [object Promise].

Despite their low usage, we also <3 [object Promise] and [object Promise]. 
```

相反，如果我们预先加载所有的浏览器数据并同步使用，它会工作:

```
const fs = require('fs').promises
const path = require('path')

const browsers = ['chrome', 'edge', 'firefox', 'safari']

async function loadBrowserData(name) {
  const data = await fs.readFile(path.resolve(__dirname, './04-data', `${name}.json`), 'utf8');
  return JSON.parse(data)
}

async function betterIdea(text) {
  const browserNameDataPairs = await Promise.all(
    browsers.map(
      async (browser) => [browser, await loadBrowserData(browser)]
    )
  );
  const browserToData = browserNameDataPairs.reduce((acc, [name, data]) => {
    acc[name] = data
    return acc
  }, {})

  let newText = text

  browsers.forEach((browser) => {
    newText = newText.replace(browser, () => {
      const {
        builtBy,
        latestVersion,
        lastYearUsage
      } = browserToData[browser];
      return `${browser} (${builtBy}, latest version: ${latestVersion}, usage: ${lastYearUsage})`
    })
  })

  return newText
}

const myText = `
We love chrome and firefox.

Despite their low usage, we also <3 safari and edge.
`;

(async () => {
  console.log(await betterIdea(myText));
})() 
```

它记录了预期:

```
We love chrome (Google, latest version: 71, usage: 64.15%) and firefox (Mozilla, latest version: 64, usage: 9.89%).

Despite their low usage, we also <3 safari (Apple, latest version: 12, usage: 3.80%) and edge (Microsoft, latest version: 18, usage: 4.30%). 
```

## 进一步阅读

*   关于 Node.js 文档中的非阻塞 I/O:[nodejs.org/en/docs/guides/blocking-vs-non-blocking/](https://nodejs.org/en/docs/guides/blocking-vs-non-blocking/)
*   泰勒·麦金尼斯的《异步 JavaScript:从回调到承诺，再到异步/等待》

是关于这个主题的好读物。理解异步 JavaScript 行为的秘密在于实验:将回调转化为承诺，反之亦然。

[通过 GitHub repo](https://speakerdeck.com/hugodf/async-javascript-history-patterns-and-gotchas) 在 SpeakerDeck 或[上查看原始幻灯片。](https://github.com/HugoDF/async-js-presentation)

如果你需要帮忙，请告诉我🙂。