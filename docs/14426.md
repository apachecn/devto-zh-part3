# 异步与同步编程

> 原文：<https://dev.to/hardy613/asynchronous-vs-synchronous-programming-23ed>

> 时间是一种幻觉。午餐时间更是如此。
> 
> *   道格拉斯·亚当斯，银河系漫游指南

## 简介

在编程中，同步操作会阻塞指令，直到任务完成，而异步操作可以在不阻塞其他操作的情况下执行。异步操作通常通过触发事件或调用提供的回调函数来完成。

## 分解 JavaScript

Javascript 有一个:

*   调用堆栈
*   WebAPI
*   事件循环
*   回调队列

调用栈是你的程序将要做的直接工作。

```
let i = 0 // declare a mutable variable
i += 1 // add one to the variable
console.log(i) // log the variable 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，声明一个变量、给变量加 1 和记录变量是添加到调用堆栈中的三个独立的指令。WebAPI 是运行 JavaScript 的环境中可用的方法。在浏览器中，`window`及其方法是 WebAPI 的一部分。当 WebAPI 完成时，它将回调放到回调队列中。

事件循环等待调用堆栈完成加载的工作。一旦事件循环注意到调用堆栈被清除，它将从回调队列向调用堆栈添加工作。考虑带有一个定时器 T1 和一个 T2 函数的 T0，该函数在声明变量之前使用变量。

```
window.setTimeout(() => console.log(i), 0)
let i = 0 
i += 1 
```

Enter fullscreen mode Exit fullscreen mode

我们得到了正确的答案`1`，而不是错误，这是因为使用`console.log`的函数是第一个 WebAPI 指令`window.setTimeout`的参数。当定时器完成时，回调函数被移动到回调队列。一旦 Callstack 清除了对变量的声明和加 1，我们的函数就被调用，使用变量就安全了。

## 从回调开始

一旦回调被添加到调用堆栈中，它就会执行。在前面的例子中，这依赖于一个定时器来完成，但是其他的 API 会有其他的条件。一个 NodeJs 例子:

```
const fs = require('fs')
const content = 'Logging to a file'
fs.writeFile('test.txt', content, err => {
    if (err) {
        throw err
    }
    console.log('logs completed')
})
console.log('end script') 
```

Enter fullscreen mode Exit fullscreen mode

一旦 writeFile API 完成，就会调用回调:

*   打开或创建文件
*   写入文件
*   关闭指定位置的文件

`fs.writeFile`是异步的，因此`console.log('end script')`在工作写文件完成之前被调用。

同步做这个动作需要什么变化？

```
const fs = require('fs')
const content = 'Logging to a file'
try {
    fs.writeFileSync('test.txt', content)
    console.log('logs completed')
} catch (err) {
    throw err
} 
```

Enter fullscreen mode Exit fullscreen mode

一个`try {} catch() {}`和同步写文件功能`writeFileSync`的使用。如果`err`被抛出，则`console.log`不被调用。

## 同步操作

运行的同步操作会阻止下一个操作，直到它完成。由于计算机速度很快，阻塞操作可能并不总是一个问题。例如:创建一个数组并记录数组中的值。

```
Array
    .from({ length: 5 }, (v, i) => i + 1)
    .forEach(value => console.log(value)) 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果长度为 5000，则需要更长时间才能记录阵列。时间上的差异是线程被锁定更长时间的结果。对资源进行同步调用会导致很长的响应时间，直到资源响应才会锁定用户界面。举个例子:

```
const request = new XMLHttpRequest()
request.open('GET', 'https://httpstat.us', false)
request.send(null)

if (request.status === 200) {
  console.log(request.responseText)
} 
```

Enter fullscreen mode Exit fullscreen mode

向您自己的服务(如数据库)发出请求也会有同样的效果。一个普通的网页在特殊的情况下会有很多请求，作为一个开发人员，您会希望这些请求尽快开始，但仍然允许页面的其余部分加载它所能加载的请求。这是异步操作变得强大的时候。

## 异步操作

异步操作独立于主程序流发生。异步代码的一个常见用途是查询数据库并使用结果。传入回调是与响应或错误进行交互的一种方式。

```
const database = require('thecoolestnewestdbframework')
database('table')
    .select('*')
    .asCallback((err, res) => {
        if (err) {
            throw err
        }
        // do something with the result
    }) 
```

Enter fullscreen mode Exit fullscreen mode

当数据库加载并响应请求时，页面的其余部分或其他资源无法加载。

### 承诺和异步操作

承诺是与异步代码交互的另一种方式。在上面的例子中，如果 const 数据库返回一个承诺，那么我们可以写:

```
const database = require('thecoolestnewestdbframework')
database('table')
    .select('*')
    .then(res => {
        // do something with the result
    })
    .catch(err => throw err) 
```

Enter fullscreen mode Exit fullscreen mode

承诺代表异步发生的工作。当 Promise 解析时，结果可以作为错误被捕获，或者在 then 方法中使用。然后返回一个承诺，这意味着然后链接返回另一个承诺到下一个然后。

```
const database = require('thecoolestnewestdbframework')

database('table')
    .select('*')
    .then(res => {
        // do something with result
        return somethingDifferent
    })
    .then(res => {
        return database('other_table')
            .select('*')
            .where('id', res)
    })
    .then(res => {
        // do something else
    })
    .catch(err => throw err) 
```

Enter fullscreen mode Exit fullscreen mode