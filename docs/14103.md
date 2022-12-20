# 承诺，承诺

> 原文：<https://dev.to/thorning_m/promises-promises-30pp>

与许多其他语言不同，JavaScript 是单线程的，这意味着它一次只能处理一个命令。我们经常需要从其他来源获取数据，无论是数据库、服务器还是用户，我相信你可以想象如果我们每次等待时都不得不暂停程序的执行，我们的应用程序会有多糟糕。承诺为我们解决了这个问题，我们请求一些数据，无论我们从哪里得到它，我们设置一旦数据返回，我们想要运行的动作。这使得我们的单线程可以自由地同时执行其他任务。

直到最近，还需要使用第三方库来实现承诺(jQuery 的 *Deferred* 是最流行的),但是从 ES6 开始，它们已经是 JS 的原生库了。下面是基本模式:

```
const promise = new Promise(resolve =>
  setTimeout(() => resolve('I waited for 2 seconds'), 2000)
)

promise.then(message => console.log(message))
// I waited for 2 seconds 
```

首先，我们从`Promise`构造函数创建一个新的`promise`对象。这将一个函数作为参数，当您希望解析承诺时，调用该参数时会执行一个函数。为了简单起见，我在这个例子中使用了一个`setTimeout`，但是你通常会在这里调用一个 API 或者查询一个数据库(在[节点](https://nodejs.org/))。之后，我们可以使用 promise 的`then`方法在 Promise 被解析时执行一些事情。

## 错误处理

这就是你如何使用承诺...只要不出问题！上面的代码没有处理 API 返回错误状态的潜在结果。幸运的是，处理错误非常简单:

```
const promise = new Promise((resolve, reject) =>
  setTimeout(() => {
    const chaos = Math.random()
    if(chaos > 0.5) {
      reject('Too much chaos!')
    } else {
      resolve('I waited for 2 seconds')
    }
  }, 2000)
)

promise.then(
  message => console.log(message),
  errorMessage => console.error(errorMessage)
) 
```

`Promise`构造函数中调用的函数实际上是用两个回调函数调用的；第二个是拒绝承诺。同样，`then`也接受第二次回调，如果承诺被拒绝，该回调就会执行。

## 许诺链接

有可能在一个承诺之后连锁`then`函数。这里也是给你介绍`catch`的好地方。在链接时，通常情况下，您将只使用`then`来处理承诺，可以在链的末尾添加一个`catch`来捕捉从它前面的任何一点抛出的错误。

在这里，我们将从另一个来源得到我们的承诺；我经常使用 Axios 来发出 HTTP 请求。他们的 API 非常简单，要发出 GET 请求，您只需编写`axios.get('api/endpoint/url')`即可，它返回一个承诺，当数据从服务器返回时，这个承诺就会得到解决。

```
axios.get('api/endpoint/url')
  .then(response =>
    saveData(response)
    axios.get('api/endpoint/url2')
  )
  .then(response2 =>
    saveData(reponse2)
  )
  .catch(error =>
    handleError(error)
  ) 
```

这里，我们用第一次 API 调用的响应调用假想的`saveData`函数，然后向不同的端点发出另一个请求。如果在这个链中的任何地方抛出一个错误，它将被`catch`函数捕获，该函数将错误对象传递给另一个虚构的函数- `handleError`。

Promise 还有一个可以添加到链中的`finally`方法，这对 spinners 来说非常方便！

```
showSpinner()
axios.get('api/endpoint/url')
  .then(response =>
    saveData(response)
    axios.get('api/endpoint/url2')
  )
  .then(response2 =>
    saveData(reponse2)
  )
  .catch(error =>
    handleError(error)
  )
  .finally(() =>
    hideSpiner()
  ) 
```

## 异步/等待

现在(自 2017 年以来)有了一种比将`then`功能链接到承诺上更干净的方式。上面的代码可以用 async/await 编写，如下:

```
async function getData() {
  const data1 = await axios.get('api/endpoint/url')
  saveData(data1)
  const data2 = await axios.get('api/endpoint/url2')
  saveData(data2)
} 
```

我们将关键字`async`添加到函数声明的前面，然后在任何想要暂停执行的地方使用`await`来等待承诺的解析。

不幸的是，async/await 的错误处理不像以前那么干净，而且(据我所知)实现与上面相同级别的错误处理的唯一方法是将所有内容包装在一个`try/catch`块中:

```
async function getData() {
  try {
    const data1 = await axios.get('api/endpoint/url')
    saveData(data1)
    const data2 = await axios.get('api/endpoint/url2')
    saveData(data2)
  } catch(error) {
    handleError(error)
  }
} 
```

## 异步编程

上面的例子工作得非常好，但是，除非我们需要等待第一次调用的结果，以便我们可以使用响应中的数据进行第二次调用，否则这是一种非常低效的编程方式。我可以用一些代码说明这一点。

首先，让我们创建一个模拟的 HTTP GET 方法:

```
function get(response, delay) {
  return new Promise(resolve => {
      setTimeout(() => resolve(response), delay * 1000)
  })
} 
```

这个函数只是在等待第二个参数`delay`设置的时间后，返回我们用第一个参数设置的`response`。我们还需要能够为我们将要编写的测试用例计时，所以让我们制作一个秒表:

```
const stopwatch = (() => {
  let start, end
  return {
    start() {
      start = Date.now()
    },
    stop() {
      end = Date.now()
      return (end - start) / 1000
    }
  }
})() 
```

我在这里使用了闭包，如果你不了解闭包，那么你应该看看我关于它们的帖子。接下来，我们将重新创建上面的函数:

```
const testCases = {

  one() {
    return new Promise(resolve => {
      const responses = []
      get('three', 3)
        .then(res => responses.push(res))
        .then(() => get('two', 2))
        .then(res => responses.push(res))
        .then(() => get('one', 1))
        .then(res => responses.push(res))
        .then(() => {
          const output =responses.join('  ')
          resolve(output)
        })
    })
  },

  two() {
    return new Promise(async resolve => {
      const first = await get('three', 3)
      const second = await get('two', 2)
      const third = await get('one', 1)
      const output = `${first}  ${second}  ${third}`
      resolve(output)
    })
  }
} 
```

希望你能认出前面的代码，只是这里我们做了三个`get`请求，而不是两个。`testCases.one`将所有数据推入一个数组，而`testCases.two`使用`await`在将数据分配给局部变量之前等待承诺解决。注意，我们创建了一个`new Promise`,然后发出`get`请求，其中每次都返回一个承诺。当我们收到了所有的数据，我们解决了我们的“主人”的承诺。所以现在，剩下要做的就是添加运行这些测试的代码:

```
async function runTest(testCase) {
  let result, time;
  stopwatch.start()
  result = await testCases[testCase]()
  time = stopwatch.stop()
  console.log('Result: %s, Time: %s seconds', result, time)
} 
```

这个函数是用我们想要运行的测试的名称调用的，它启动秒表，然后运行测试。当测试承诺完成时，秒表停止计时，结果记录到控制台。让我们运行我们的测试:

```
runTest('one') // Result: three two one, Time: 6.003 seconds
runTest('two') // Result: three two one, Time: 6.004 seconds 
```

所以你可以看到我们的两个函数都花了六秒钟运行，这是因为我们已经同步编写了代码；每一行都是按顺序执行的，并且会等待前面的行完成后再自己运行。相反，我们可以发挥 JavaScript 的优势，异步编写代码。我们将向我们的`testCases`对象添加第三个测试:

```
three() {
    return new Promise(resolve => {
        const responses = []

      function check() {
        if(responses.length > 2) {
            resolve(responses.join('  '))
        }
      }

      get('three', 3).then(res => {
        responses.push(res)
        check()
      })
      get('two', 2).then(res => {
        responses.push(res)
        check()
      })
      get('one', 1).then(res => {
        responses.push(res)
        check()
      })
    })
  } 
```

这个函数还有更多的内容。首先我们初始化空的`responses`数组，然后我们添加了一个名为`check`的函数。接下来，我们像以前一样发出三个`get`请求，只是这次我们每次解析一个请求时都调用`check`。`check`查看我们的`responses`数组的长度，当我们的数组包含三个响应时，它解析我们的‘主’承诺。来看看效果如何:

```
runTest('three') // Result: one two three, Time: 3.002 seconds 
```

有一半的时间，你可以看到我们的响应顺序已经改变，我们的代码正在异步运行！

## Promise.all()

有一种更好的方法来编写第三个测试用例，这样我们就不需要`check`函数了。我们也可以把我们的回答放回和其他测试一样的顺序；在现实世界中，这可能并不重要，但无论如何让我们这样做吧！

坦白的时候到了，我忘了提一件关于承诺的非常重要的事情。承诺总是处于三种状态之一。当您第一次创建承诺时，它处于“待定”状态，然后会转换为“已解决”或“已拒绝”状态。一旦承诺达到“已解决”或“已拒绝”，就不能再回到“待定”状态。如果你想知道一个承诺处于哪种状态，你可以调用`Promise.state()`，这在调试时非常有用，因为你可以设置一个断点并在控制台中运行它(我可能很快会在 Chrome 中发布一篇关于调试的帖子)。

这是第四个测试用例:

```
four() {
  return new Promise(resolve => {
    const responses = []
    responses.push(get('three', 3))
    responses.push(get('two', 2))
    responses.push(get('one', 1))
    Promise.all(responses)
      .then(values => {
        const output = values.join('  ')
        resolve(output)
      })
  })
} 
```

在这个函数中，我们将从`get`调用返回的承诺直接推入到`responses`数组中。这些承诺处于“待定”状态，并将作为占位符，这意味着结果将与我们的前两个测试用例的顺序相同。我们可以使用 promise 的`all`方法，而不是每次解析 promise 时都检查`responses`数组的长度，该方法本身返回一个 Promise，当数组中的所有 Promise 都解析时，该 Promise 就会解析。这比以前好多了，因为我们不再需要知道我们在等待多少承诺:

```
runTest('four') // Result: three two one, Time: 3.003 seconds 
```

三秒钟，这一次结果的顺序是正确的。如果您想自己运行测试，下面是上面的运行代码:

* * *

希望这篇文章对你有用，感谢你花时间阅读。如果你有任何意见，更正或问题，那么你可以在 Twitter 上联系我。👍