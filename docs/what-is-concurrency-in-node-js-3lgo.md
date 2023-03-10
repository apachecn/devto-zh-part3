# Node JS 中的并发是什么？

> 原文：<https://dev.to/neutrino2211/what-is-concurrency-in-node-js-3lgo>

Javascript 是一种单线程语言，这在某些情况下可能非常有限，因为进程被卡在一个线程上执行，无法充分利用它所运行的 CPU，但由于并发性，它的单线程本质不是什么问题。

***但是等等，什么叫并发！？*T3】**

我很高兴你问了(*即使你没有问，也要假装你问了，我们将继续讨论*😉)

### 基础知识

并发意味着两个或更多的进程在一个线程中一起运行，但不是同时运行，我们很多人在 Node JS 中遇到过并发，但可能没有注意到它( *Prime example = me* 😅).

示例:

你可以运行这段代码！！

`const fs = require('fs'); fs.writeFile('./file.txt', 'Hello World!!', function(){ console.log('Wrote "Hello World!!" into file.txt'); }); console.log('Writing "Hello World!!" into file.txt');`

上例中的代码对我们大多数人来说一定很熟悉，但是您知道这是并发性的一个主要例子吗？。我们都同意第 7 行在第 5 行之前执行，对吧，**这就是并发！**，多个独立的进程在同一个线程中运行，轮流执行代码。

这些是执行过程中采取的步骤。

*   fs.writeFile 调用一个底层函数，该函数充当 JS 和 C++之间的代理

*   该函数调用 C++代码，该代码在事件循环上创建一个处理写操作的进程

*   console.log('编写“Hello World！!"into file . txt’)

*   该过程将内容写入`file.txt`

*   流程返回，我们的回调被执行

*   console.log('写了“Hello World！!"into file . txt’)

这很好，但是用并发行为编写代码有一个副作用，它被亲切地称为**“回调地狱”**

示例:

写一个文件，然后从中读取。

`const fs = require('fs'); fs.writeFile('./file.txt', 'Hello World!!', function(){ console.log('Wrote "Hello World!!" into file.txt'); fs.readFile('./file.txt', function(err, data){ if(err){ throw new Error(err); } console.log('Read "', data.toString(), '" from ./file.txt') }) }); console.log('Writing "Hello World!!" into file.txt');`

你越需要使用这样一个函数提供的数据，情况就越糟糕，但是当你使用**承诺**时，整个痛苦就可以避免。

### 承诺

承诺是 javascript 结构，它“承诺”异步代码的解决/失败，并帮助我们以一种**语法上**同步的方式处理它们的成功/失败。

示例:

`const fs = require('fs'); const readPromise = function(){ return new Promise(function(resolve, reject){ fs.readFile('./file.txt', function(err, data){ if(err){ reject(err); } resolve(data); }) }); } const writePromise = function(){ return new Promise(function(resolve, reject){ fs.writeFile('./file.txt', 'Hello world!!', function(err){ if(err){ reject(err); } resolve(); }) }); } writePromise() .then(() => { return readPromise() }) .then(data => console.log(data.toString())) .catch(err => console.log(err));`

上面的代码看起来并没有好到哪里去，但是随着承诺而来的还有 async/await 关键字，这对清理我们的代码非常有帮助。

`await`关键字帮助我们检索由 promise 解析的数据，就好像它是从同步函数直接返回的一样，但是`await`只在异步函数中工作，这就是`async`关键字的用武之地，它帮助我们定义可以使用`await`的异步函数。

示例:

`const fs = require('fs'); const readPromise = function(){ return new Promise(function(resolve, reject){ fs.readFile('./file.txt', function(err, data){ if(err){ reject(err); } resolve(data); }) }); } const writePromise = function(){ return new Promise(function(resolve, reject){ fs.writeFile('./file.txt', 'Hello world!!', function(err){ if(err){ reject(err); } resolve(); }) }); } async function start(){ await writePromise(); // data returned as if it were from a synchronous function const data = await readPromise(); console.log(data.toString()); }; start()`

这就是干净的异步代码！！

### 更进一步

既然我们可以创造承诺并实现它们，我们就不再需要使用回调。这里有一些一般的例子。

**注意:Node JS 中的默认库不太支持承诺，所以我们将使用第三方库来实现异步示例**

#### API 调用

使用回调

`const http = require('http'); http.request('http://jsonplaceholder.typicode.com/todos/1', function(res) { let data = ''; res.setEncoding('utf8'); res.on('data', function (chunk) { data += chunk; }); res.on('end', function(){ console.log(JSON.parse(data)); }) }).end();`

利用承诺

`const fetch = require('node-fetch'); async function start(){ const response = await fetch('http://jsonplaceholder.typicode.com/todos/1'); const data = await response.text(); console.log(JSON.parse(data)); } start();`

#### 产卵过程

使用回调

`const { spawn } = require('child_process'); const ls = spawn('echo', ['Hello World!!']); let data = ''; ls.stdout.on('data', (_data) => { data += _data; }); ls.on('close', (code) => { console.log(data); });`

利用承诺

`const spawn = require('spawn-promise'); async function start(){ const out = await spawn('echo',['Hello World!!']); console.log(out.toString()); } start();`

## 结论

并发是一件美好的事情，尤其是在速度是重中之重的大规模应用中，我希望这篇文章能帮助你更多地了解它以及如何最好地应用它。

## 感谢阅读！！！

*考虑在 [Twitter](https://twitter.com/neutrino2211) 上关注我，你可以查看我之前的帖子[这里](https://dev.to/neutrino2211/how-to-avoid-javascript-bugs-31pm)*