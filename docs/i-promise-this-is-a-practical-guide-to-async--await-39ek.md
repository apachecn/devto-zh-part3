# 我保证这是异步/等待的实用指南

> 原文：<https://dev.to/lampewebdev/i-promise-this-is-a-practical-guide-to-async--await-39ek>

有了 ES8，我们有了另一种方式来编写异步代码，这种方式比回调的 Async / Await 更具可读性。对于 ES6，我们已经得到了承诺。为了理解 Async / Await，我们首先要理解承诺。

### 承诺

```
const resolveAfter2Seconds = () => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve();
    }, 2000);
  });
}

resolveAfter2Seconds()
    .then(() => { console.log('resolved') })        // this gets executed 
    .catch(() => { console.log('some error') });    // this does not get executed

const rejectAfter2Seconds = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject();
    }, 2000);
  });
}

rejectAfter2Seconds()
    .then(() => { console.log('resolved') })        // this does not get executed
    .catch(() => { console.log('some error') });    // this gets executed 
```

resolveAfter2Seconds 函数将返回一个新的承诺。每个承诺都有一个状态。初始状态是`pending`。之后可以换成`fulfilled`或者`rejected`。当它为`fulfilled`时，它会将值从 resolve 传递给 then 函数，然后你可以对它做任何你想做的事情。如果状态变为`rejected`，那么它将运行 catch()函数。我希望承诺的基本要素现在已经很清楚了。

### 问题

给定以下代码:

```
const resolveAfterXSeconds = (ms) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(ms);
    }, ms);
  });
}

resolveAfterXSeconds(2000)
    .then((ms) => { console.log(`resolved after ${ms}`) });
resolveAfterXSeconds(1000)
    .then((ms) => { console.log(`resolved after ${ms}`) }); 
```

这段代码会在大约 2 秒或 4 秒内完成吗？什么时候我们会看到 console.log()？那么这段代码是顺序的、并发的还是并行的呢？

### 答案

这段代码是真正并行的。它将执行这两个函数，然后返回第二个函数调用，因为超时只有 1000 ms，然后返回第一个函数调用，因为这里超时是 2000。所以你得想想这是不是真的是你想要的。可能这些函数调用是互相依赖的吧！所以这不是你真正想要的。

我见过的一个解决方案如下:

```
resolveAfterXSeconds(2000)
  .then((ms) => { 
    console.log('promise in the first then');

    resolveAfterXSeconds(1000).then((ms) => { console.log(`resolved after ${ms}`) })

    return ms;
  }).then((ms) => { console.log(`resolved after ${ms}`) }); 
```

我们首先调用带有 2000 的函数，一旦它被解析，我们立即调用带有 1000 的函数，然后我们返回第一个函数的 ms。回报等于承诺。resolve()，这就是它在这里工作的原因。因此，这将是连续运行，但它不是非常可读，并提醒我，我们希望避免回调地狱。

但是 Promise.all()呢？我们来看一个例子:

```
Promise.all([resolveAfterXSeconds(2000), resolveAfterXSeconds(1000)]).then((ms) => {
  console.log(`resolved after ${ms[0]}`);
  console.log(`resolved after ${ms[1]}`);
}); 
```

这段代码是并发的，因为 Promise.all()创建了一个单个的 Promise，当它所依赖的所有 Promise 都被解析时，这个单个的 Promise 也被解析，正因为如此，两个 resolveAfterXSeconds 函数被同时调用，但是当所有的 Promise 都被满足时，then()函数被调用。然后，您将收到一个包含已解析承诺的数组。数组中每个解析值的顺序与承诺传递给 Promise.all()函数的顺序相同。如果有 2 个 API 调用，这种模式很好。例如，一个用于用户数据，一个用于位置信息，然后可以将它们组合成一个对象。

我们需要所有这些信息来更好地理解 Async / Await！

### 异步/等待

让我们最终转向异步/等待！首先:Async / Await 不能完全代替承诺。Async / Await 通常更容易阅读，但也容易被误解。我们的第一个例子:

```
resolveAfterXSeconds = (ms) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(ms);
    }, ms);
  });
}

start = async () => {
  const first = await resolveAfterXSeconds(2000);
  console.log(first);
  const second = await resolveAfterXSeconds(1000);
  console.log(second);
}
start(); 
```

所以我们仍然使用旧的 resolveAfterXSeconds 函数，这里没有任何变化。现在我们创建一个新的函数，叫做 start，这是第一个新的东西，arrow 函数之前的 async。只有 async () => {}会返回一个函数。调用这个函数将返回一个承诺。这里要记住的重要一点是，如果承诺只是回报一些东西，它就会立即实现。在下一行，我们也有新的东西。await 告诉 javascript 它必须在这里等待，直到右边的承诺解决或拒绝，直到这个函数被暂停。在我们的示例中，第一次调用 resolveAfterXSeconds 函数需要 2 秒钟，然后它将运行 console.log，然后运行第二个 resolveAfterXSeconds 函数。因此，运行我们的启动功能大约需要 3 秒钟。最后，我们得到了我们想要的！javascript 中顺序运行的异步代码！

我们从中了解到，Async / await 与 promise 不一样。在编码时记住这一点很重要。你必须为正确的工作使用正确的工具。

Async / Await 也可以用在 promise.all.
这样的并发样式中

```
resolveAfterXSeconds = (ms) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(ms);
    }, ms);
  });
}

concurrentStart = async () => {

  const first = resolveAfterXSeconds(2000);
  const second = resolveAfterXSeconds(1000);

  console.log(await first);
  console.log(await second);
} 
```

唯一改变的是 await 现在在 console.log()本身中。为什么现在这个是并发的？因为第一个和第二个都已经开始，现在我们只是在等待两个都完成，因为记住异步创建一个承诺。如果你回想一下 Promise.all()，那么这个例子和这个例子完全一样。

### 让我们来点实际的

#### 获取 API

让我们来看看获取 API。fetch(URL)将返回一个新的承诺，所以我们可以等待它，但我们现在正在处理网络函数，我们不知道它们是否曾经解决或是否只是被拒绝。所以我们需要处理错误。

```
fetchUserNames = async (endpoint) => {
  try {
    const response = await fetch(endpoint);
    let data = await response.json();
    return data.map(user => user.username);
  } catch (error) {
    throw new Error(error);
  }
}

start = async () => {
  userNames = await fetchUserNames('https://jsonplaceholder.typicode.com/users');
  console.log(userNames);
  fetchUserNames('https://wrong.url').catch(error => console.log(error));
}

start(); 
```

您可以在 Async / Await 函数中使用 Try / Catch 来实现更好的错误处理。顺便提一下:nodejs 将会退出带有未捕获错误的进程！在这里，您可以将返回值视为决心，将抛出视为承诺中的拒绝。然后我们使用获取 API 来获取数据。正如您看到的，fetch()调用返回一个承诺。因为我们知道我们得到的是一个我们正在调用的 JSON。json()在响应上，然后它自己再次为我们返回一个承诺，这就是为什么我们在这里也需要 await。然后我们只是提取用户名并返回新创建的数组。我们的 start 函数需要异步，因为 await 只能在异步函数中调用。我在这里混合故意等待，并承诺给你看，你可以使用两者！

#### koajs 新快递

```
app.get("/", async (request, response, next) => {
  try {
    const finalResult = await database.getStuff();
    response.json(finalResult);
  } catch (error) {
    next(error);
  }
}); 
```

如果你曾经使用过 expressjs，你就会知道这是怎么回事。koajs 和 expressjs 是由同一个开发人员开发的，但是它是从头开始构建的，使用 es6+特性。此外，只要有意义，它就会使用承诺。在这个例子中，我们在'/'路由上处理一个 HTTP GET 请求。如您所见，该路由器可以是异步。那么我们就可以在箭头函数中做任何我们想做的事情。在这个例子中，你必须想象我们正在调用数据库来获取一些数据，然后将它发送回客户端。

### 每 X 秒运行一次功能

```
const waitFor = (ms) => new Promise(r => setTimeout(r, ms));

const start = async () => {
  try {
    console.log("start");
    c = 1;
    while(c < 10){
      console.log('waiting!');
      await waitFor(2000)
      console.log('start some function');
      await runDBBackup();
      if(c === 3){
        throw new Error('An error happend');
      }
      c++
    }
    console.log('end');
  } catch (error) {
    console.log(`error: #{error}`);
  }
}

start(); 
```

好了，这里是我们所学的一切。首先，我们需要在一个承诺中包装 setTimeout，它将在 X 秒后解析。就是这样！它什么也没做。它只是暂停执行。然后我们创建我们的开始函数。在这种情况下，我在运行 3 次后故意让它失败。这就是为什么我们有 c 变量。然后我们将进入 while 循环，等待 2 秒钟。然后，我们将运行我们的备份功能，当它运行第四次时，将发生错误。如果用 just true 替换 c < 10，只要没有异常，就会一直运行。这是一个备份过程的简单实现，将在 X 时间后运行。

**感谢阅读！**

说你好！[insta gram](https://www.instagram.com/lampewebdev/)|[Twitter](https://twitter.com/lampewebdev)|[LinkedIn](https://www.linkedin.com/in/michael-lazarski-25725a87)|[Medium](https://medium.com/@lampewebdevelopment)