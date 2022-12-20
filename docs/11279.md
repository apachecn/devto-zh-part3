# 闭包上的一些闭包

> 原文：<https://dev.to/jckuhl/some-closure-on-closures-44ga>

# 简介

对于许多新的 JavaScript 开发人员来说，闭包是一个需要一段时间来适应的概念。这是 JavaScript 更抽象的概念之一。当函数中有一个函数可以访问外部函数的状态时，闭包就存在了。

什么？

看，当有人定义一个闭包时，我总是看到这个定义。但是它的真正含义并不清楚，所以让我来解释一下

# 执行上下文

当一个函数被调用时，JavaScript 的引擎会创建一个所谓的*执行上下文*。该上下文包含该功能所需的所有状态。简单来说，状态就是变量和它们的当前值。

```
function foo() {
    const a = 3;
} 
```

在上面的函数`foo()`中，当你调用`foo()`时，一个执行上下文被创建，变量`a`被设置为`3`，然后函数结束，上下文被销毁，变量被销毁，函数返回`undefined`。

> 这也是 scope 的工作方式。`a`在函数中声明，并且只存在于该函数和可能出现在该函数中的任何较低的块中。

`foo()`内的任何内部函数都可以访问`foo()`的状态。

```
function foo() {
    const a = 3;
    function log() {
        console.log(a);
    }
    log();
} 
```

但这只是基本的范围界定，对吗？是的，在这个例子中，但是这就是闭包的强大之处。如果外部函数`foo()`被破坏，内部函数`log()`(如果它被带出函数)将仍然可以访问`foo()`的状态。

```
function foo() {
    const a = 3;
    function log() {
        console.log(a);
    }
    return log;
}

const log = foo();
log();    // logs 3

// foo()() would also be acceptable, and would also log 3 
```

内部的`log()`函数仍然有`foo()`的执行上下文，即使`foo()`被调用、创建和销毁。

为了进一步说明这一点，让`foo()`接受一个参数，而不是一个硬编码的变量。

```
function foo(a) {
    function log() {
        console.log(a);
    }
    return log;
}

const log3 = foo(3);
const log4 = foo(4);

log3();    //logs a '3'
log4();    //logs a '4'

// alternatively
foo('hello')();    //logs 'hello' 
```

在这里你可以看到`foo()`被调用了 3 次，使用了不同的`a`值，返回的函数仍然从执行上下文中“记住”了`a`的值。

这就是终结的本质。它是一个内部函数，可以访问外部函数的状态。

# 但是为什么呢？

我为什么要用这个？嗯，在很多情况下使用闭包是有用的。发电机使用封闭。事件处理程序使用闭包。使用闭包函数的部分应用。闭包是函数式编程的主要组成部分。

以下是用 JavaScript 创建生成器的方法。这个类似于(但比)Python 的`range()`对象:

> 顺便说一下，这是一个手工制作的生成器，而不是 JavaScript 自带的内置生成器语法。关于官方 JavaScript 生成器的更多信息，请查看 Mozilla 开发者网络

```
function range(start, end, step=1) {
    let count = 0;
    return () => {
        count += 1;
        if(start < end) {
            return start += count !== 1 ? step : 0;
        } else {
            return false;
        }
    }
}

const r = range(1, 5);
console.log(r());    // logs 1
console.log(r());    // logs 2
console.log(r());    // logs 3
console.log(r());    // logs 4
console.log(r());    // logs 5
console.log(r());    // logs false 
```

> 三元组`count !== 1 ? step : 0;`用于确保范围包括起始值

`range()`函数返回一个匿名函数，它跟踪传递给函数的三个参数的当前状态。每次调用`r()`，它将返回该状态的下一次迭代，该状态被表达式`start += step`变异。从这个 range 对象开始，使用闭包将许多 JavaScript 数组函数重写为在生成器上工作的函数并不十分困难。

下面是`map()`可能的样子。

```
function map(mapping) {
    return (range)=> ()=> {
        const value = range();
        if(value && value !== false) {
            return mapping(value);
        } else {
            return false;
        }
    }
}

const squares = map(x => x ** 2)(range(1,5));
console.log(squares());    //logs 1
console.log(squares());    //logs 4
console.log(squares());    //logs 9
console.log(squares());    //logs 16
console.log(squares());    //logs 25
console.log(squares());    //logs false 
```

> 注意箭头符号`()=> ()=> {}`是闭包的样子。

这里有一个生成器来创建平方数。每次调用该函数时，它都会“记住”外部函数的执行上下文。

当然，你也可以在发电机上循环。

```
let s;
while(s = squares()) {
    console.log(s);
} 
```

> 不要与 while 循环中的表达式混淆。那个`=`是赋值运算符，不是相等运算符。然而，`=`是一个函数，它将返回`squares()`的值，该值要么是一个数字，要么是 false，因此我们可以在这里利用 truthy/falsy。

但是我觉得写出来更清楚。

您可以在 [Repl.it](https://repl.it/@jkuhl87/WheatTameWebportal) 看到这些生成器的代码

# 保存状态

当您需要保存状态时，闭包也可以工作。假设您有一个需要连接到多个 mongo 数据库的大型应用程序。我有一个快速后端，我需要将多个连接函数导出到多个 javascript 文件。闭包可以是一种简单的方法:

```
//in a file called "database.js"
const mongoose = require('mongoose');

const user = process.env.MONGO_USER;
const password = process.env.MONGO_PW;
const db1URI = `mongodb+srv://${user}:${password}@cluster1.mongodb.net/database1?retryWrites=true`;
const db2URI = `mongodb+srv://${user}:${password}@cluster2.mongodb.net/database2?retryWrites=true`;
const db3URI = `mongodb+srv://${user}:${password}@cluster3.mongodb.net/database3?retryWrites=true`;

// wrap the connection in a closure so I can export it with the URI
function Connect(uri) {
    return function() {
        mongoose.connect(uri, {
            auth: {
                user,
                password
            },
            useNewUrlParser: true
        });

        const db = mongoose.connection;
        db.on('error', console.error.bind(console, 'connection error'));
        db.once('open', ()=> {
            console.log('\nSuccessfully connected to Mongo!\n');
        });
    }
}

const db1Connect = Connect(db1URI);
const db2Connect = Connect(db2URI);
const db3Connect = Connect(db3URI);

module.exports = {
    db1Connect,
    db2Connect,
    db3Connect
}; 
```

然后在你的快速代码的不同模块中，你可以说

```
const MongooseConnect = require('./database.js');
MongooseConnect.db1Connect();

//and in another file somewhere else
const MongooseConnect = require('./database.js');
MongooseConnect.db2Connect();

//etc 
```

在这里，`Connect()`方法保存了在闭包中传递的 URI 参数，以便稍后当您实际调用它时，它可以用那个 URI 连接到 Mongo(通过 Mongoose)。这允许我有一个单一的连接函数和一个中心位置，所有的连接字符串都集中在一个地方。我可以简单地导出一个函数并将字符串作为参数传递，但是我必须在使用`Connect()`函数的不同文件中定义一个连接字符串，或者在另一个文件中定义一个对象，将所有连接字符串放在一个位置。使用闭包，我可以简单地导出函数，并将所有的连接字符串放在一个地方，这样我就可以轻松地维护它们。

# 事件

闭包也适用于异步操作和事件。事实上，当您将回调传递给点击处理程序时，根据定义，这就是一个闭包。是一个函数，传递给它的处理程序就是闭包。

这里是我写的一段代码，当它最终点击了闭包是如何为我工作的:

```
function clicker() {
    let counter = 0;
    const myDiv = document.getElementById("mydiv");
    const btn = document.querySelector("button");
    btn.addEventListener('click', ()=> {
        myDiv.innerHTML = counter;
        counter++;
    });
}

clicker(); 
```

我需要将事件监听器添加到一个函数的 DOM 元素中，我写了上面的代码来确保这个概念本身是可行的。这是一个简单的计数器，你按一下按钮，数字就会上升。我猜是万岁。

但关键是，匿名点击事件处理程序仍然可以访问`counter`变量和`myDiv`元素，即使在用户点击按钮时`clicker()`函数已经销毁了它的执行上下文(除非我想他有超快的毫秒级反应速度。)即使`counter`和`myDiv`的作用域是`clicker()`，事件处理程序仍然可以访问它们。

异步函数和事件与闭包一起工作得很好，因为闭包*仍然*可以访问封闭函数的状态，即使在封闭函数的销毁和闭包的调用之间有一段时间。如果有一些闭包调用了一些网络 API，并且需要 250 毫秒才能得到响应，那么这没什么，闭包仍然可以访问封闭状态。

# 总结

闭包很难理解。希望上面的一些例子能更清楚地说明它们是如何工作的。记住，闭包只是一个内部函数，它可以访问包含它的函数的状态。这里有一个更好地处理闭包的想法，使用我上面提供的`range()`函数，并尝试创建一个`toArray()`方法，为生成器中的每个值提供一个数组。或者尝试创建一个`filter()`函数，或者修改其他 JavaScript 数组方法来处理`range()`。我自己做了一些，它们都要求你使用闭包。

谢谢你，祝你编码愉快。