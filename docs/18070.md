# 用 Node.js 读/写 JSON 文件

> 原文：<https://dev.to/eojthebrave/readwrite-json-files-with-nodejs-58e4>

# 用 NodeJS 读/写 JSON 文件

[![Photo by Patrick Tomasso on Unsplash](img/c6a522e5d29b9cf87d300e477c4ecbb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ztwMtNzI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_792F4E89A8BA29B00D0171E7988A150253BE3C8514EFBBF8C15D90EEE7BDE9AE_1539096615415_patrick-tomasso-71909-unsplash.jpg)

乔恩·丘奇和乔·辛德勒写的。[最初发表在](https://medium.com/@lullaboteducation/read-write-json-files-with-node-js-92d03cc82824)媒体上。

当您想在服务器重启和 Node 之间存储数据时， [JSON](https://developer.mozilla.org/en-US/docs/Glossary/JSON) 文件是一个简单方便的选择。无论是读取配置文件还是为应用程序持久化数据，Node 都有一些内置的实用程序，可以轻松地读写 JSON 文件。

在您的应用程序中使用 JSON 文件可能是持久化数据的一种有用方式。我们将看看几种不同的处理 JSON 文件的方法。

在本教程中，我们将:

*   从磁盘读取 JSON 数据
*   学习使用`fs`模块与文件系统交互
*   将数据保存到 JSON 文件中
*   使用`JSON.parse`和`JSON.stringify`在 JSON 格式之间转换数据

本教程结束时，你应该能够使用 Node 的内置`fs`模块处理 JSON 文件。

## 目标

假设您有一个保存在磁盘上的 *customer.json* 文件，其中保存了您商店中一位客户的记录。
作为你的商店应用程序的一部分，你想访问客户的地址，然后在下订单后更新订单数。

在本教程中，我们将了解如何读写我们的 *customer.json* 文件。

```
// customer.json
{
    "name": "Mega Corp.",
    "order_count": 83,
    "address": "Infinity Loop Drive",
} 
```

## 用`fs`处理文件

访问 Node 中的文件是通过[本机模块](https://nodejs.org/api/fs.html) `[fs](https://nodejs.org/api/fs.html)`完成的，它为您提供了监视、读取和写入文件的功能，以及许多其他使用文件系统的工具。因为它是一个本机模块，所以我们可以在代码中需要它，而无需安装它。就叫`const fs = require('fs')`。
`fs`模块为我们提供了许多功能的同步或异步版本的选择。同步版本会阻止其他代码的执行，直到它们完成对文件系统的访问、读取或写入数据。异步函数将在不阻塞其他代码的情况下运行。你可以在这里了解更多关于同步/异步行为的信息[。](https://nodejs.org/en/docs/guides/blocking-vs-non-blocking/)

这种同步行为在某些地方很有用，比如在启动时，在运行任何其他代码之前读取配置文件，但是在 web 服务器中使用时，当同步文件读取正在运行时，所有传入的请求都将被阻塞，这就成了一个大问题。出于这个原因，您通常希望在代码中使用异步版本的`fs`函数。我们将重点关注异步操作，但也会展示同步操作。

为了用`fs`异步读写文件，我们将使用`fs.readFile`和`fs.writeFile`。
我们还将使用全局`JSON`助手将对象转换成 JSON 字符串，并将 JSON 字符串转换成对象。

**读取 JSON 文件**
读取 JSON 文件最简单的方法就是请求它。传递带有 JSON 文件路径的`require` `()`将同步读取数据并解析成 JavaScript 对象。

```
const config = require('./config.json') 
```

但是用 require 读取 JSON 文件有它的缺点。该文件将只被读取一次；再次请求它将返回第一次运行`require`时的缓存数据。这对于在启动时加载静态数据(比如配置数据)来说很好。但是对于读取磁盘上发生变化的文件，就像我们的 *customer.json* 可能做的那样，我们需要使用异步`fs.readFile`手动读取文件。

**读取文件用** `**fs.readFile**`
来访问客户的地址，我们需要:

*   从文件中读取 JSON 数据
*   将 JSON 字符串解析成 JavaScript 对象

为了从 *customer.json* 文件中加载数据，我们将使用`fs.readFile`，向它传递文件的路径、可选的编码类型和接收文件数据的回调。

如果文件被成功读取，内容将被传递给回调。

```
const fs = require('fs')

fs.readFile('./customer.json', 'utf8', (err, jsonString) => {
    if (err) {
        console.log("File read failed:", err)
        return
    }
    console.log('File data:', jsonString) 
}) 
```

*   。/config.json”是文件的相对路径
*   “utf8”是我们正在读取的文件编码的可选参数，可以省略
*   `(err, jsonString) => {}`是文件被读取后运行的回调函数

现在我们有了 JSON 字符串形式的文件内容，但是我们需要将字符串转换成一个对象。

在我们可以在代码中使用回调数据之前，我们必须将它转换成一个对象。`JSON.parse`将 JSON 数据作为输入，返回一个新的 JavaScript 对象。否则，我们只会得到一串带有我们无法访问的属性的数据。

如果传递了无效的 JSON 字符串，`**JSON.parse**` **会抛出异常错误并使我们的程序崩溃。为了防止崩溃，我们将** `**JSON.parse**` **包装在** `**try catch**` **语句中，以优雅地捕捉任何错误。**

这个例子展示了如何读取和解析一个 JSON 文件:

```
const fs = require('fs')

fs.readFile('./customer.json', 'utf8', (err, jsonString) => {
    if (err) {
        console.log("Error reading file from disk:", err)
        return
    }
    try {
        const customer = JSON.parse(jsonString)
        console.log("Customer address is:", customer.address) 
                // => "Customer address is: Infinity Loop Drive"

    } catch(err) {
        console.log('Error parsing JSON string:', err)
    }
}) 
```

使用读取 *customer.json* 中的`jsonString`，我们创建一个对象，并可以访问地址属性。如果`JSON.parse`抛出一个错误，我们在`catch`块中处理它。
现在我们在 *customer.json* 文件中有了数据的对象表示！

我们也可以使用`fs.readFileSync`同步读取文件。`readFileSync`在读取文件后返回文件内容，而不是进行回调。

```
try {
    const jsonString = fs.readFileSync('./customer.json')
    const customer = JSON.parse(jsonString)
} catch(err) {
    console.log(err)
    return
}

console.log(customer.address) // => "Infinity Loop Drive" 
```

我们可以使用这些知识创建一个可重用的 helper 函数来读取和解析 JSON 文件。
这里我们创建了一个名为`jsonReader`的函数，它将为我们读取并解析一个 JSON 文件。它采用文件的路径和一个回调来接收解析的对象和任何错误。它将为我们捕捉由`JSON.parse`抛出的任何错误。

```
const fs = require('fs')

function jsonReader(filePath, cb) {
    fs.readFile(filePath, (err, fileData) => {
        if (err) {
            return cb && cb(err)
        }
        try {
            const object = JSON.parse(fileData)
            return cb && cb(null, object)
        } catch(err) {
            return cb && cb(err)
        }
    })
}

jsonReader('./customer.json', (err, customer) => {
    if (err) {
        console.log(err)
        return
    }
    console.log(customer.address) // => "Infinity Loop Drive"
}) 
```

## 用`fs.writeFile`写入文件

将 JSON 写入文件系统类似于读取它。我们将使用`fs.writeFile`将数据异步写入一个 *newCustomer.json* 文件。
首先，要将数据写入 JSON 文件，我们必须用`JSON.stringify`创建数据的 JSON 字符串。这将返回一个 JavaScript 对象的 JSON 字符串表示，它可以被写入文件。类似于在读取文件时将数据解析成对象，我们必须将数据转换成字符串才能将其写入文件。

所以我们用下面的数据创建了一个客户对象，并把它转换成一个字符串。

```
const customer = {
    name: "Newbie Corp.",
    order_count: 0,
    address: "Po Box City",
}

const jsonString = JSON.stringify(customer)
console.log(jsonString) 
    // => "{"name":"Newbie Co.","address":"Po Box City","order_count":0}" 
```

如果你试图将一个对象写入一个文件而不将其字符串化，你的文件****将会是空的，看起来像这样:**** 

```
[object, object] 
```

一旦数据被字符串化，我们可以使用`fs.writeFile`创建一个新的客户文件。
我们向`fs.writeFile`传递文件路径、要写入的客户数据以及一个在文件写入后执行的回调。如果 *newCustomer.json* 文件不存在，将创建该文件；如果确实存在，就会被覆盖！

**下面是一个用** `**fs.writeFile**` **:**
写 JSON 文件的例子

```
const fs = require('fs')

const customer = {
    name: "Newbie Co.",
    order_count: 0,
    address: "Po Box City",
}

const jsonString = JSON.stringify(customer)

fs.writeFile('./newCustomer.json', jsonString, err => {
    if (err) {
        console.log('Error writing file', err)
    } else {
        console.log('Successfully wrote file')
    }
}) 
```

就是这样！一旦回调运行，文件就被写入磁盘。注意:我们只传递了一个错误对象；我们写的 filedata 不会传递给回调函数。

我们也可以使用`fs.writeFileSync` :
以同样的方式同步写一个文件

```
const jsonString = JSON.stringify(customer)

fs.writeFileSync('./newCustomer.json', jsonString) 
```

在你的文件写完之后，它看起来会像这样:

```
{"name":"Newbie Co.","address":"Po Box City","order_count":0} 
```

默认情况下，字符串化将所有数据放在一行中。或者，您可以通过将缩进的空格数传递给`JSON.stringify` :
来使输出文件可读

```
const jsonString = JSON.stringify(customer, null, 2) 
```

上面，我们告诉 stringify 将数据缩进两个空格。

现在你的输出文件应该是这样的:

```
{  "name":  "Newbie Co.",  "address":  "Po Box City",  "order_count":  0  } 
```

**更新 JSON 文件**
现在我们能够读写我们的客户文件，我们可以将它们作为一种简单的数据库来使用。如果我们想更新 JSON 文件中的数据，我们可以读取内容，更改数据，然后将新数据写回文件:

```
jsonReader('./customer.json', (err, customer) => {
    if (err) {
        console.log('Error reading file:',err)
        return
    }

    // increase customer order count by 1
    customer.order_count += 1

    fs.writeFile('./customer.json', JSON.stringify(customer), (err) => {
        if (err) console.log('Error writing file:', err)
    })
}) 
```

肯定不是您可以选择的最有效的数据库，但是像这样处理 JSON 文件是在项目中持久化数据的一种简单方法。

**包装**
JSON 是 Node 中最常见的数据类型之一，能够读写 JSON 文件非常有用。您已经学习了如何使用`fs.readFile`和`fs.writeFile`来异步处理文件系统，以及如何解析 JSON 格式的数据，并从`JSON.parse`中捕捉错误。

你可以使用`require`在启动时读取一个 JSON 文件来同步解析一行 JSON 文件。现在您可以使用一个简单的 JSON 文件作为数据存储。

如果你想了解更多，你可以阅读一下 [JSON 实际上是什么](https://developer.mozilla.org/en-US/docs/Glossary/JSON)，并找到更多关于[同步与异步](https://stackoverflow.com/questions/16336367/what-is-the-difference-between-synchronous-and-asynchronous-programming-in-node)代码的信息。

* * *

Lullabot Education 正在加强我们的 Node.js 培训。注册我们的[简讯](https://www.getdrip.com/forms/662782570/submissions/new)并了解即将推出的指南和教程——帮助塑造 Node.js 教育的未来。**