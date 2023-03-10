# 一次拿这么多东西

> 原文：<https://dev.to/aravindballa/fetch-so-many-things-at-once-5b34>

[![0](img/7b0685a95ee8063dbe74e74fdca8cb44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N6a1xERK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://source.unsplash.com/random/800x600/%3Fnature%2Cfood)

Node 中有一个`fetch` API，它允许我们发出一个 HTTP 请求并从服务器获取一些信息。我们可以用它来进行 REST 调用，获取网页的 HTML 内容(如果我们使用 node 来抓取的话)以及更多的事情。

> 本文对任何返回承诺的函数都有效。

这种调用的一个例子是这样的

```
fetch('/url')
  .then(res => res.json())
  .then(data => console.log(data)); 
```

# 异步方式

我们可以使用 async 和 await 做同样的事情。

```
const result = await fetch('/url');
const data = await result.json();

console.log(data);

// Or, a one-liner
// const data = await (await fetch('/url')).json(); 😉 
```

# 我有很多东西要拿！

好吧。我们可以通过一个经典的 for 循环来实现。同步性质将被保留。我是说，我们可以同步地一个接一个取。

```
const urls = [...];
for(const url of urls) {
    const result = await fetch(url);
    const data = await result.json();

    console.log(data);
} 
```

但是如果顺序不重要呢？我们可以一次把它们都拿来。是的，一次完成，使用 Promise API。毕竟，`fetch`返回一个承诺，这就是为什么我们`await`要解决它。

Promise API 有这个方法`Promise.all()`，可以等待它接受的所有承诺作为要解决的参数。

```
const urls = [...];
const promises = urls.map(url => fetch(url));

await Promise.all(promises);

for (const promise of promises) {
    const data = await promise.json();
    console.log(data);
} 
```

这会节省我们很多时间。想象一下，我们想要解析大约 100 个网页，每个网页需要 2 秒钟来获取和搜集我们需要的信息。如果我们一个接一个地获取它，大约需要 200 秒，也就是 3 分多钟。但是如果我们一次全部取回来，不到一分钟就可以了。

# 喜欢的，真的很多！

什么是我们有超过 10000 个网址获取。如果我们做和上面一样的事情，我们很可能不会成功。我们将不得不面对一些奇怪的套接字挂起错误。我们能做些什么呢？

有一个名为`Bluebird`的节点包，它有自己的 Promise API，功能相同。它有一个名为`map`的方法，这个方法带有一个额外的选项参数，我们可以在这里设置并发性。

`Promise.map(urls => fetch(url), { concurrency: 100 });`

正如我们从该行可以推断的那样，这将同时一次获取 100 个请求。这将节省大量的 CPU 负载。

```
const Promise = require('bluebird').Promise;
const urls = [...];
const promises = await Promise.map(
    urls => fetch(url),
    { concurrency: 100 }
);

for (const promise of promises) {
    const data = await promise.json();
    console.log(data);
} 
```

谢谢你坚持到最后。

继续黑！✌