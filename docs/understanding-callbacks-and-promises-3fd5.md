# 理解回访和承诺

> 原文：<https://dev.to/_ferh97/understanding-callbacks-and-promises-3fd5>

这两个概念是 Javascript 编程语言的基础。因为这种语言在异步编程的范例下工作。

所以，我决定分享这篇文章，让大家了解什么是回拨和承诺。执行异步操作的两个特征。

所以，我们走吧👍

# 回调

为了理解回调，我将做一个简单的类比。

> 假设我们正在打电话。谈话时，出现情况要立即解决。我们保留电话，做我们必须做的事情，当我们结束时，我们回到我们保留的电话。

嗯，通过这个例子，我们可以大致了解一下，什么是回调。基本上顾名思义就是。

现在用编程语言来说。

一个**回调是一个当异步操作完成时将被执行的函数。**

一个**回调作为一个参数传递给一个异步操作**。通常，这是作为函数的最后一个参数传递的。这样做是一个很好的实践，所以要记住这一点。

回调结构看起来是这样的:

```
function sayHello() {
    console.log('Hello everyone');
}

setTimeout(sayHello(), 3000) 
```

我们在上面的例子中所做的是，首先定义一个将消息打印到控制台的函数。之后，我们使用一个名为 **setTimeout** 的定时器(这个定时器是一个原生的 Javascript 函数)。这个定时器是一个异步操作，在一定时间后执行回调。在本例中，3000 毫秒(3 秒)后将执行 sayHello 功能。

### 回调模式

正如我们在开始时提到的，作为优秀的开发人员，我们应该将回调位置作为一个参数。**应该总是放在最后。**这就是所谓的**回调模式。**

通过这种方式，我们的代码将更具可读性，并且在其他程序员处理它时更容易维护。

我们来看另一个回调例子:

```
const fs = require('fs') // Importing Nodejs library

// Declaring file path
const filePath = './users.json'

// Asynchronous operation to read the file
fs.readFile(filePath, function onReadFile(err, result) {
    // In case of error print it in the console
    if (err) {
        console.log('There was an error: ' + err)
        return // Get out of the function
    }
    // Print on the console the file and the content of it.
    console.log('The file was successfully read it: ' + result)
}) 
```

这里，我们使用一个 Nodejs 库，用于在我们的文件系统上进行操作。在这个例子中，我们使用 readFile 函数从我们的计算机中读取一个文件。这个函数接收两个参数(文件路径和回调)。正如我们所注意到的，名为 onReadFile it 的回调被作为最后一个参数找到。

> 匿名声明回调是很常见的，但是为它指定一个名称是很好的做法，以防出错，以便更容易识别它。

最后，回调将一直执行，直到我们的代码读取完所请求的文件。如果存在，Javascript 将在此过程中继续执行代码。

* * *

### 回调地狱

一旦你知道回调是如何工作的，并付诸实践，我们必须记住一些事情。作为一名优秀的开发人员，我们必须知道如何使用它，并避免回调地狱之类的丑陋事情。

**回调地狱就是回调的误用**。看起来是这样的:

```
fs.readdir(source, function (err, files) {
  if (err) {
    console.log('Error finding files: ' + err)
  } else {
    files.forEach(function (filename, fileIndex) {
      console.log(filename)
      gm(source + filename).size(function (err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err)
        } else {
          console.log(filename + ' : ' + values)
          aspect = (values.width / values.height)
          widths.forEach(function (width, widthIndex) {
            height = Math.round(width / aspect)
            console.log('resizing ' + filename + 'to ' + height + 'x' + height)
            this.resize(width, height).write(dest + 'w' + width + '_' + filename,             function(err) {
              if (err) console.log('Error writing file: ' + err)
            })
          }.bind(this))
        }
      })
    })
  }
}) 
```

基本上，嵌套回调的使用是一个不好的实践，并且在视觉上产生了一种金字塔，正如我们所看到的。这变成了一个难以维护和阅读的代码，我们不希望这样。

#### 如何避免回调地狱？

*   命名函数:正如我之前说过的，你可以做的第一件事就是命名你的函数(回调)。因此，当一个错误产生时，它会以特定的方式用函数名来指示错误。此外，您允许您的代码更具描述性，当其他程序员阅读它时，他们更容易维护它。

*   模块化:一旦命名了函数，就可以开始通过分离来定义它们。这样，你只会把回调名。首先，从在同一个文件中定义它们开始，在文件的底部。然后，另一种选择是将该函数写在一个单独的文件中。这样，我们可以在任何文件中导出和导入它。

这允许代码的可重用性、更好的可读性和易于维护。

*   处理错误:在编写代码时，我们必须记住错误总是会发生的。为了能够容易地识别它们，编写处理可能发生的错误的代码是非常重要的。

通常，在回调中，错误作为第一个参数传递。我们可以用下面的方式处理错误:

```
const fs = require('fs')

const filePath = './users.json'

fs.readFile(filePath, handleFile)

function handleFile(err, result) {
    if (err) {
        return console.log('There was an error: ' + err)
    }
    console.log('File: ' + result)
} 
```

应用好的代码实践，会让其他程序员一辈子都不会讨厌你！

* * *

# 承诺

[![Promises image](img/52138e469dbe3652b1eb27d39acf6022.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nViHo068--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A-C6_By6RL9BW5z4-)

Javascript 中的承诺就是承诺。我们知道，当我们做出承诺时，就意味着我们将尽一切可能达到预期的结果。但是，我们也知道，由于某些原因，承诺不能总是兑现。

就像承诺在现实生活中一样，它在 Javascript 中，以另一种方式表示；用代码。

让我们来看一个承诺的例子:

```
let promise = new Promise(function(resolve, reject) {
    // things to do to accomplish your promise

    if(/* everything turned out fine */) {
        resolve('Stuff worked')
    } else { // for some reason the promise doesn't fulfilled
        reject(new Error('it broke'))
    }
}) 
```

promise 是 Javascript 的原生类(从 ES6 开始)。

承诺的构造函数接收一个参数:回调，它有两个参数:

*   解决
*   拒绝

这些都是 Javascript 中已经定义的函数，所以我们不应该自己构建它们。

这个回调函数将这两个函数作为参数，称为执行器。

创建承诺时，执行程序会立即运行。

#### 这个 executor 函数要执行什么？

好吧，在这个里面，我们会放上所有必要的代码来实现我们的承诺。

一旦执行器完成执行，我们将发送一个函数作为参数。

*   如果满足，我们使用*解析*函数。

*   万一由于某种原因失败，我们使用*拒绝*功能。

函数 resolve 和 reject 只接收一个参数。正如我们在前面的例子中看到的，reject 函数传递一个带有 error 类的错误是很常见的。

承诺有三种独特的状态:

*   挂起:异步操作尚未完成。

*   已完成:异步操作已完成并返回值。

*   拒绝:异步操作失败，并指出失败的原因。

promise 对象有两个属性:

*   状态:指明承诺的状态。
*   结果:如果履行了承诺，则存储承诺的值，如果拒绝，则存储错误。

最初，承诺的状态是“待定”,结果是“未定义”。

一旦承诺完成执行，承诺的状态和结果将被修改为相应的值。这取决于承诺是完成了还是被拒绝了。

让我们看下面的图表来更好地理解它:

[![Promise fulfilled](img/e5dc58a8dc5ed3083498cd0a5ef21dc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KoxhcOzA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dcko79fnp/image/upload/v1548806160/promises-properties.png)

[![Promise rejected](img/ecd031d14c08a59d77b9cbf88bb32bf0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--El34jacm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dcko79fnp/image/upload/v1548806160/promises-properties_1.png)

一旦承诺改变了状态，就不能逆转。

#### 如何消费或者叫承诺？

为了消费我们创建的承诺，我们使用 then 和 catch 函数。在代码中，它们看起来像这样:

```
promise.then(function(result) {
    console.log(result)
}).catch(function(err) {
    console.log(err)
}) 
```

函数 *then* 将允许我们处理已经完成或履行的承诺。

函数 *catch* 将允许我们处理被拒绝的承诺。

在*然后*函数中，我们还可以处理被拒绝的承诺。为此，处理程序接收两个参数。第一个是万一承诺兑现，第二个是万一被拒绝。这样:

```
promise.then(function(result) { // Handling the value
    console.log(result)
}, function(err) { // Handling the error
    console.log(err)
}) 
```

*then* 和 *catch* 处理程序是异步的。

基本上，一旦 Javascript 读完下面的代码，就会执行 *then* 和 *catch* 。

示例:

```
promise.then(function(result) {
    console.log(result)
}).catch(function(err) {
    console.log(err)
})

console.log('Hello world') 
```

我们可以认为，首先它会印在承诺的价值或错误上。但是知道它们是**异步操作**，我们必须记住**将花费最少的时间来执行**，因此首先显示消息“Hello world”。

* * *

Promise 类有一个名为 *all* 的方法，用于执行一组承诺。它看起来像这样:

```
Promise.all([
    new Promise.((resolve, reject) => setTimeout(() => resolve(1), 3000)), // 1
    new Promise.((resolve, reject) => setTimeout(() => resolve(2), 2000)), // 2
    new Promise.((resolve, reject) => setTimeout(() => resolve(3), 1000)), // 3
]).then(result => console.log(result)) // 1, 2, 3 
```

*然后*处理程序将在控制台中打印每个承诺的结果数组。
如果其中一个承诺被拒绝，该功能将被拒绝并出错。如下图所示:

```
Promise.all([
    new Promise.((resolve, reject) => setTimeout(() => resolve(1), 3000)), // 1
    new Promise.((resolve, reject) => setTimeout(() => resolve(2), 2000)), // 2
    new Promise.((resolve, reject) => setTimeout(() => reject(new Error('An error has ocurred')), 1000))
]).then(result => console.log(result))
.catch(err => console.log(err)) // An error has ocurred 
```

还有一种方法类似于 *all* ，但有区别。这是*族*的方法。

与 *all* 函数相同，它接收一组承诺，但它将首先返回已完成或被拒绝的承诺。让我们看一个代码的例子:

```
let promise1 = new Promise(function(resolve, reject) {
    setTimeout(function() {
        resolve('promise one')
    }, 3000) // Resolve after 3 seconds
})

let promise2 = new Promise(function(resolve, reject) {
    setTimeout(function() {
        resolve('promise two')
    }, 1000) // Resolve after 1 seconds
})

Promise.race([
    promise1,
    promise2
]).then(result => console.log(result)) // promise two 
```

我们可以看到，返回给我们的价值只是第二个承诺的回应。这是因为第二个承诺先执行。
让我们看看另一个被拒绝的承诺的例子:

```
let promise1 = new Promise(function(resolve, reject) {
    setTimeout(function() {
        resolve('promise one')
    }, 3000) // Resolve after 3 seconds
})

let promise2 = new Promise(function(resolve, reject) {
    setTimeout(function() {
        resolve('promise two')
    }, 2000) // Resolve after 2 seconds
})

let promise3 = new Promise(function(resolve, reject) {
    setTimeout(function() {
        reject('promise three rejected')
    }, 1000) // Reject after 1 second
})

Promise.race([
    promise1,
    promise2,
    promise3
]).then(result => console.log(result))
.catch(err => console.log(err)) // promise three is rejected 
```

在这段代码中， *race* 函数，将要打印的是它在我们声明的第三个承诺中发现的错误。你已经可以想象为什么了。实际上，第三个承诺比其他承诺先执行。

所以， *race* 方法，不管承诺是被拒绝还是被完成，都会执行第一个，忽略其他的。

* * *

到目前为止，我希望我已经让自己明白了关于回访和承诺的内容。基本上，Javascript 的这两个特性用于处理异步操作。这就是这种语言的基础，也是它受欢迎的原因。

我将在另一篇文章中继续讨论处理异步的最后一个功能。异步等待。