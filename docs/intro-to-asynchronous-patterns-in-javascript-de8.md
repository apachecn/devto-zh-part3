# JavaScript 异步模式简介

> 原文：<https://dev.to/kpollich/intro-to-asynchronous-patterns-in-javascript-de8>

*JavaScript 异步编程简史*

JavaScript 通常被称为“默认异步”，但是异步代码的处理方式在 JavaScript 的生命周期中已经发生了变化。这些年来，我们已经从**回调**到**承诺**到**异步/等待**，但是每一种方法都以这样或那样的方式与它的前辈相关。

## 异步 vs 同步

首先，让我们快速弄清楚异步和同步代码之间的区别。当代码是同步的时，它是按“行顺序”执行的，这意味着由代码定义的每个任务在进入下一个任务之前被执行完成。在 JavaScript 中，可能是这样的意思。

```
console.log("Hello world")

const name = "kyle";
console.log("It's me, " + name)

console.log("Some lovely code :)") 
```

Enter fullscreen mode Exit fullscreen mode

所有这些代码同步执行——在程序运行到下一行之前，每一行都执行完毕。

异步编程以相反的方式工作:在前一行执行完成之前，程序移动到下一行*。在这篇文章中，我们将深入探讨一些例子，但是涉及异步代码的常见情况是数据库连接、HTTP 请求以及程序可能必须等待来自其他来源的响应的其他情况。*

这里有一个很好的 [StackOverflow](https://stackoverflow.com/questions/748175/asynchronous-vs-synchronous-execution-what-does-it-really-mean/26804844#26804844) 答案，可以很好地弥补这种差异:

> 同步的
> 
> 你在排队买电影票。除非你前面的每个人都拿到了，否则你是拿不到的，这同样适用于排在你后面的人。
> 
> 异步的
> 
> 你和许多其他人在一家餐馆里。你点你的食物。其他人也可以点他们的食物，他们不必等你的食物做好并端给你后再点。在厨房餐厅里，工人们不停地烹饪、上菜和点菜。食物一做好，人们就会得到服务。

对这些编程概念有了一个很好的基本理解之后，让我们深入了解 JavaScript 作为一种编程语言在其整个生命周期中是如何处理异步代码的。

## 回调

回调是 JavaScript 和其他异步语言中的一个基本概念。因为 JavaScript 非常依赖事件和异步处理，所以回调是该语言的核心。JavaScript 实现了[高阶函数](https://eloquentjavascript.net/05_higher_order.html#h_xxCc98lOBK)，这意味着函数可以存储在命名变量中，并作为参数传递给其他函数。作为参数传递给另一个函数的函数通常称为回调。回调是 JavaScript 中处理异步调用的最原始、最古老的方式。

`setTimeout`是接受回调函数的一个最简单的例子:

```
setTimeout(function () {
  console.log('It has been a second!')
}, 1000) 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个例子，说明如何实现自己的方法来接受回调:

```
function validateInput (input, callback) {
  var result = { errors: [] }

  if (!input.name || input.name.length < 6) {
    result.errors.push('Invalid name')
  }

  if (!input.email) {
    result.errors.push('Email must be provided')
  }

  callback(result)
}

validateInput({ name: 'Kyle', email: 'kyle@example.com' }, function (result) {
  if (result.errors.length) {
    console.error('Whoops')
  } else {
    console.log('Hooray')
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

当你必须将几个都接受回调的函数链接在一起时，很容易陷入[“回调地狱”](http://callbackhell.com/)。考虑一些节点代码，其中我们连接到 MySQL 并使用标准回调来运行一些依赖于其他查询的返回值的查询。

```
var config = require('./config.json')
var mysql = require('mysql')

// Note: this is inefficient and bad on purpose to prove a point :)
function updateUserEmail (oldEmail, newEmail, callback) {
  var connection = mysql.createConnection(config)

  connection.connect()

  connection.query('SELECT id FROM users WHERE email = ?', [oldEmail], function (error, results) {
    if (error) {
      throw(error)
    }

    var userId = results[0].id

    connection.query('SELECT is_active FROM users WHERE user_id = ?', [userId], function (error, results) {
      if (error) {
        throw(error)
      }

      var isActive = results[0].is_active

      if (!isActive) {
        throw new Error('Error - user is inactive')
      }

      connection.query('UPDATE users SET email = ? WHERE id = ?', [newEmail, userId], function (error, results) {
        if (error) {
          throw(error)
        }

        if (results[0].affectedRows === 0) {
          throw new Error('Error - failed to update user')
        }

        connection.query('SELECT * FROM users WHERE id = ?' [userId], function (error, results) {
          if (error) {
            throw(error)
          }

          callback(results[0])
        })
      })
    }
  })

  connection.end()
}

try {
  updateUserEmail('kyle@example.com', 'kyle2@example.com', function(changedUser) {
    console.log(changedUser)
  })
} catch (error) {
  console.error(error)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 承诺

`Promise`是一个代表异步操作最终结果的对象。承诺可以是带值的`resolved`或`rejected`，它们类似于 C#或 Java 等其他语言中的`Tasks`或`Futures`。

我们可以用一个构造函数实例化一个`Promise`,这个构造函数采用一个类似于这样的函数

```
new Promise((resolve, reject) => {
  if (foo) {
    return resolve('foo')
  }

  reject('not foo')
}) 
```

Enter fullscreen mode Exit fullscreen mode

承诺通过三种状态进行转换:`pending`、`fulfilled`和`rejected`。我们可以使用`then`方法来执行有意义的操作。`catch`方法用于捕捉拒绝。

```
somePromise(foo)
  .then(data => {
    console.log(data)
  })
  .catch(error => {
    console.error(error)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

承诺可以被链接起来，错误最终会“冒泡”到一个单独的`catch`处理程序，这使得它们对于减少嵌套和统一您的范围非常强大。

```
somePromise(foo)
  .then(data => {
    return transformData(data)
  })
  .then(newData => {
    if (newData.bar) {
      return logData(newData)
    }

    return logSomethingElse(newData)
  })
  .catch(error => {
    console.error(error)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

承诺是清理充满回调的代码的强大模式。下面是上面用承诺改写的 MySQL 调用的例子。

```
const config = require('./config.json')
const mysql = require('mysql2/promise')

function updateUserEmail (oldEmail, newEmail ) {
  mysql.createConnection(config)
    .then(connection => connection.execute('SELECT id FROM users WHERE email = ?', [oldEmail])
    .then([{ id }] => {
      this.userId = id

      return connection.execute('SELECT is_active FROM users WHERE user_id = ?', [userId])
    })
    .then([{ is_active }] => {
      if (!is_active) {
        throw new Error('Error - user is inactive')
      }

      return connection.execute('UPDATE users SET email = ? WHERE id = ?', [newEmail. this.userId])
    })
    .then(() => connection.execute('SELECT * FROM users WHERE id = ?', [this.userId])
    .then([user] => user)
}

updateUserEmail('kyle@example.com', 'kyle2@example.com')
  .then(changedUser => console.log(changedUser))
  .catch(error => console.error(error)) 
```

Enter fullscreen mode Exit fullscreen mode

## 异步/等待

Async/Await 是 Promises 之上的一层语法糖，消除了另一层嵌套。通过将函数标记为`async`，我们可以访问`await`关键字。`await`让我们“解开”内嵌的承诺，对待悬而未决的承诺，就像它们被同步解决一样。只能*返回承诺的* `await`函数。如果你`await`一个不返回`Promise`的函数，它的结果将被包装在一个`Promise.resolve`调用中。

```
// With a Promise
function getData() {
  return fetch('example.com/api/data')
    .then(body => body.json())
    .then(data => console.log(JSON.stringify(data)))
}

// With async/await
async function getData() {
  const body = await fetch('example.com/api/data')
  const data = await body.json()

  console.log(JSON.stringify(data))
} 
```

Enter fullscreen mode Exit fullscreen mode

在 async/await 块中捕捉错误就是使用 JavaScript 的标准`try/catch`构造。与 Promises 类似，这个错误会“冒泡”，所以对于给定的异步代码块，您只需要一个`catch`块。

```
async function getData() {
  try {
    const body = await fetch('example.com/api/data')
    const data = await body.json()

    console.log(JSON.stringify(data))
  } catch (error) {
    console.error(error)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们用 async/await 重写的 MySQL 示例。通过利用返回承诺的库和接口(如 MySQL2)，您可以得到一些真正简洁的异步代码。

```
const config = require('./config.json')
const mysql = require('mysql2/promise')

async function updateUserEmail (oldEmail, newEmail ) {
  const connection = await mysql.createConnection(config)

  const userId = (await connection.execute('SELECT id FROM users WHERE email = ?', [oldEmail]))[0].id

  const isActive = (await connection.execute('SELECT is_active FROM users WHERE user_id = ?', [userId])[0].is_active)

  await connection.execute('UPDATE users SET email = ? WHERE id = ?', [newEmail. userId])

  return (await connection.execute('SELECT * FROM users WHERE id = ?', [this.userId]))[0]
}

// You actually can't use `await` in the top level scope, so you'd need to put this
// into a separate  `async` function or something in the real world
try {
  const user = await updateUserEmail('kyle@example.com', 'kyle2@example.com')
  console.log(user)
} catch (error) {
  console.error(error)
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样吧！现在您已经看到了一些异步代码的实际例子，以及 JavaScript 是如何处理这些用例的。在现代 JavaScript 中，理解每种异步模式以及它们之间的关系是很有帮助的。`async/await`无疑是最现代的异步代码方法，但是您仍然会遇到大量的回调，并且很好地理解`Promises`对于有效地利用`async/await`非常重要。

## 进一步阅读

*   [https://developer . Mozilla . org/en-US/docs/Glossary/Callback _ function](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Statements/async _ function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)