# JavaScript 中的异步操作

> 原文：<https://dev.to/kartik2406/async-operations-in-javascript-1k2c>

同步任务/程序是指每条指令都被一步一步地执行，每条指令都阻塞处理器，直到它完成执行。另一方面，异步不会阻塞处理器，而是并行执行任务，或者使用一种机制使任务看起来像是并行工作的。为了实现并行性，大多数编程语言使用线程的概念。主线程产生其他线程来做一些工作，这样主程序就不会被阻塞。

JavaScript 是一种同步语言，它是单线程的。Javascript 在事件循环的帮助下实现了并行。事件循环的工作是惊人的，但超出了本文的范围。我会推荐你去看菲利普·罗伯茨的这个演讲。他深入而有趣地解释了这个问题。简单来说，事件循环使我们程序中的不同任务看起来像是在并行执行，但事实并非如此。异步代码看起来不同，行为也不同于同步代码。如果不小心的话，你可能会面临这样的问题，比如使用了未定义的值，而不是异步操作的实际值。

### 为什么不同步呢？

嗯，如果我们只是进行同步操作，那么我们的程序，用户界面将在操作过程中没有反应。想象一下，如果不能在每次进行 API 调用时都与页面进行交互，那么所有的网站都会感到拖沓，你会变得不耐烦。如果你的程序正在处理一些 CPU 繁重的任务，其他任务也必须等待。这不是一个适合生活的好世界。输入输出操作、网络调用是异步操作的几个例子。

### 处理异步操作

有不同的机制可以帮助你处理异步操作，让我们深入了解一下。

### 回调

回调是一个在异步操作完成时执行的函数。您将回调传递给异步操作，当它完成执行时调用函数。让我们举一个从文件中读取的例子。为此，我们将使用 Nodejs 的 [fs 模块。](https://nodejs.org/api/fs.html) 

```
const fs = require('fs') // syntax to load a module
fs.readFile('/file-path', (err, data) => {
  if (err) console.log(err)
  console.log(data)
}) 
```

关于大多数操作的回调，需要注意的一点是它们的函数签名。约定是，如果发生错误，第一个参数将是一个错误对象，否则为 null/undefined，第二个参数将是操作的结果。

尽管回调帮助我们处理了异步操作，但它给我们带来了另一个问题，回调地狱。

### 回调地狱

考虑一下这种情况，您有一个文件名为 file1 的文件 2，您想从中读取数据。这个关于文件的场景很奇怪，但是在处理 API 调用时，当你需要根据第一个 API 的结果调用第二个 API 时，通常会发生这种情况。

```
const fs = require('fs') // syntax to load a module
fs.readFile('/file1', 'utf8', (err, file2) => {
  if (err) console.log(err)
  fs.readFile(`${file2}`, (err2, data) => {
    if (err) console.log(err2)
    console.log(data)
  })
}) 
```

如果你必须做另一个操作，在另一个回调函数中有回调函数，添加更多的回调函数，等等。代码变得难以查看和调试。随着代码库的增长，这将导致错误和维护问题。

### 承诺

[Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 是回调的替代品，要使用它，你需要一个返回 promise 对象的函数。承诺可以解决(成功)或拒绝(发生错误)，待定:仍在执行。

这使得语法更加简单

```
readFile('file-path')
  .then(res => {})
  .catch(err => {}) 
```

**。然后()**和**。catch()** 也返回一个承诺，这样你就可以在你的 then 中进行另一个异步操作，而不必经历回调地狱。

的。catch()块帮助您处理。then()链。

有几个操作仍然不支持承诺，在编写本文时，fs 模块不支持承诺，要解决这个问题，你最好编写一个包装器，使用 [fs-extra](https://www.npmjs.com/package/fs-extra) npm 包，或者如果你使用的是节点 8 及以上，使用 [util.promisify()](https://nodejs.org/api/util.html#util_util_promisify_original) 方法。

```
const util = require('util')
const fs = require('fs')

const readFile = util.promisify(fs.readFile)

readFile('./test.txt', 'utf8')
  .then(res => {
    console.log('File data ', res)
  })
  .catch(err => {
    console.log(err)
  }) 
```

### 异步/等待

承诺让我们的生活变得简单，而异步等待让生活变得更加简单。这是一个不同的内部语法 async-await 使用承诺、[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators#Generator_functions)。
语法非常简洁，一段异步代码看起来是同步的。随着调试变得越来越简单，这真是令人惊讶。使用它有两个步骤，在异步函数之前使用 async 关键字，使用 await 关键字等待异步函数返回数据。只能在异步函数中使用 *await* 关键字。

```
async function getData() {
  let data = await readFile("./test.txt", "utf8");
  console.log(data);
}
getData(); 
```

### 异步/等待中的错误处理

因为异步等待只是承诺，你可以在异步操作之后链接一个 catch 块，就像普通的承诺一样。如果你不喜欢这个，你也可以使用传统的试抓块。

如果您从 node 开始，我希望这篇文章能有所帮助，在以后的文章中，我们将看看异步代码在处理 API 调用时有多大帮助。如果你面临异步操作结果的问题，一定要检查你是如何处理它们的。

如果你喜欢这篇文章，请分享。

*héctor j . Rivas 在 Unsplash 上拍摄的封面照片*