# 使用 Module.exports 来组织 Node.js 代码

> 原文：<https://dev.to/eojthebrave/use-moduleexports-to-keep-nodejs-code-organized-2mhb>

# 使用 Module.exports 保持 Node.js 代码有条理

[![Photo by frank mckenna on Unsplash](img/0c04b89090665847b1cc3864d02acdc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UpTIpRu8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_892B56BE495B9FD4F289D7009ED6F7DBB7905EFB12BD11CDF71950D13D1D1F6C_1539096352900_frank-mckenna-252014-unsplash.jpg)

乔恩·丘奇和乔·辛德勒写的。[最初发表在](https://medium.com/@lullaboteducation/use-module-exports-to-keep-node-js-code-organized-9379526ebac8)媒体上。

Node 不关心我们是否将整个项目写在一个巨大的 *index.js* 文件中。但是如果你想让人们能够阅读你的代码，你需要以一种有意义并且易于维护的方式来组织它。输入节点的模块系统！

模块到底是什么？模块将相关的函数组织到一个文件中，我们可以在以后需要使用它们的时候导入它。模块将相关代码封装到一个单元中，通常存储在一个文件中。Node 中的每个文件都可以被认为是一个模块，每个文件都有自己的`module`全局对象，我们将使用它来公开代码，以便导入到另一个文件中。

在本教程中，我们将:

*   了解`module.exports`
*   从文件中导出助手函数
*   使用`require`将我们的助手函数包含在另一个文件中

本教程结束时，您应该能够使用 Node 的模块系统来创建自己的模块，这样您就可以在项目中重用代码。

## 目标

从另一个文件 *index.js* 中的 *helpers.js* 导入助手函数。

## 什么是 module.exports？

为了从文件中导出代码，我们给文件的`module.exports`对象赋值。Node 中的每个 JavaScript 文件在其全局范围中都有一个代表该特定文件的`module.exports`对象。该对象将保存我们想要从文件中导出的任何代码。我们可以给`module.exports`分配一个对象、一个函数或任何值，以便在另一个文件中再次使用。

这是 Node 中模块系统的一部分。每个文件在其作用域中都有一个名为`module`的全局对象，它保存关于该特定文件的信息。我们可以通过将该文件分配给`module.exports`来公开其中的代码。

## 什么是`require`？

Require 帮助我们加载模块。要从文件中导入代码，我们必须将文件的路径传递给 require: `require('./filepath')`。在 JavaScript 文件上调用`require`将运行文件中的代码，并返回`module.exports`的值。

这实际上非常类似于当你`require`一个 npm 模块时发生的事情。除了在这个例子中，我们传递给`require`一个文件的路径，而不是一个包的名字。当你`require`一个 npm 包时，同样的事情正在你的 *node_modules* */* 文件夹中发生，这个包被安装到这个文件夹中。

这里我们从名为`testModule.js` :
的文件中导出一个字符串

```
// testModule.js
module.exports = "This is a test!"

// index.js
const test = require('./testModule.js')
console.log(test) // => "This is a test!" 
```

在任何给定的文件中，我们可以将东西分配给`module.exports`以允许我们使用`require`语句将它们导入到另一个文件中。

当用一个文件路径调用`require`时，该文件中的代码将被求值并返回`module.exports`。

```
// helpers.js
console.log('I got required!)
module.exports = "Exported!"
// index.js
const exportedValue = require('./helpers.js') 
```

上面的代码将输出“我被要求了！”到控制台，`exportedValue`将等于`“Exported!”`。

如果我们重新运行 require 语句(在第一个实例之后)，我们仍然会得到导出的值`module.exports`，但是`console.log`不会再次运行。这是因为 require 语句被缓存；它们被运行一次，然后再次调用它们只会返回`module.exports`的值。

在某些时候，您可能还会看到这样的 ES6 代码:

```
// helpers.js
export default = "I'm an export!"

// index.js
import helpers from './helpers.js' 
```

这使用 ES6 模块语法，默认情况下，目前没有节点引擎支持此功能。如果使用类似 Babel 的东西为你传输代码，你可以使用这种风格。但是现在，要知道我们讨论的是导出的 CommonJS 语法，`module.exports`。

## 创建助手文件

所以让我们使用`module.exports`从一个`helpers.js`文件中导出一些函数来做一些简单的数学运算，然后在`require`的帮助下在另一个文件`index.js`中使用它们。
一种选择是从文件中导出一个单独的匿名函数。

**让我们导出一个将数字乘以 2 的函数，并在 index.js 文件中使用它:**

```
// helpers.js
module.exports = function(x) {
    return x * 2
}

// index.js
const helpers = require('./helpers.js')
helpers(4) // => 8 
```

我们将`module.exports`的值设置为我们想要导入的函数，并在 *index.js* 中要求它。
方便，但是如果你想一次导出几个函数呢？我们可以导出一个包含几个函数的对象文字，然后通过它们的名字来访问这些函数。这也称为命名导出，因为我们可以选择以后通过名称只导入我们想要的属性。

```
// helpers.js
module.exports = {
    multiplyByTwo: function(x) { return x *2 },
    divideByTwo: function(x) { return x / 2}
}

// index.js
const helpers = require('./helpers.js')
helpers.multiplyByTwo(10) // => 5
// or, you can import just the named property you need
const divideByTwo = require('./helpers.js').divideByTwo
divideByTwo(18) // => 9 
```

要求文件返回我们用`module.exports`导出的对象。您也可以通过在 require 语句后使用点语法，仅导入您需要的属性。

我们可以使用不同的语法，而不必在一个地方声明所有的函数。这里，我们直接导出`module.exports`对象上的一个命名属性。

```
// helpers.js
module.exports.multiplyByTwo = function(x) { return x * 2 }
module.exports.divideByTwo = function(x) { return x / 2 }
function nonExportedFunction(x) {
    return x * 3
}
// index.js
const helpers = require('./helpers.js/)
const divideByTwo = require('./helpers.js').divideByTwo 
```

在上面的代码中，`module.exports`看起来与我们在前一个例子中导出的对象一样。两者都将是一个带有两个键的对象，`multiplyByTwo`和`divideByTwo`。唯一的区别是我们一次分配一个。

**注意:**确保不要将上述语法与直接导出对象混淆。如果您稍后在文件中给`module.exports`赋值，它将覆盖您已经导出的任何内容。

```
// helpers.js
module.exports.multiplyByTwo = function(x) { return x * 2 }
module.exports.divideByTwo = function(x) { return x / 2 }
module.exports = "I just overwrote all my exports! Don't mix these styles!" 
```

## 向模块传递参数

我们可以通过使用一些 curry 来和我们的数学助手玩一玩。Currying 是来自函数式编程的一个概念:你调用一个带有变量的函数，然后得到另一个已经嵌入了该变量的函数。如果你愿意，这是一种配置。这是一个简单的说法，但它可能非常强大。

让我们把数学函数集中在一个数字上进行运算。当需要文件时，我们可以传入这个数字，并返回与最初传递给 curry 函数的数字相乘、相除或相加的函数。

```
// curryHelpers.js
module.exports = function(x) {
    return {
    multiply: function(y) { return y * x },
    divide: function(y) { return y / x },
    add: function(y) { return y + x }
    }
}

// index.js
const byTwo = require('./curryHelpers.js')(2)
const byTen = require('./curryHelpers.js')(10)

byTwo.multiply(5) // => 10
byTwo.divide(14) // => 7
byTwo.add(9) // => 11

byTen.multiply(5) // => 50 
```

这是一个使用`require`和`module.exports`将变量传递到文件范围的例子。我们通过调用由`require`返回的函数将数字 2 传递到 curryHelpers 文件的作用域中。我们导出的函数对象现在被配置为将传递给它的任何数与我们传入的初始数 2 相乘、相除和相加。我们对数字 10 做了同样的事情，得到了一个用数字 10 配置的函数对象。

## `exports`为`module.exports`的简称

最后可以用`module.exports`的简写。对象`exports`也是全局可用的，并且指向与`module.exports`相同的对象。

所以下面的代码是等价的:

```
// assigning named exports
module.exports.pizza = true
exports.pizza = true

// exporting an object
module.exports = {}
exports = {} 
```

## 包装完毕

Node 是一种非常模块化的语言，您可以通过使用`module.exports`来帮助导出和重用项目中的代码，从而充分利用这一点。使用`require`，您可以将代码导入到项目中的任何其他文件中。导入模块的想法在 Node 中很常见，理解`module.exports`将有助于你理解这种模块化方法。

我希望你能够在你的代码中使用这些模式！

如果你想了解更多关于`module.exports`和它是如何工作的，你可以了解一下[模块作用域](https://stackoverflow.com/questions/15406062/in-what-scope-are-module-variables-stored-in-node-js)和[通用](https://blog.risingstack.com/node-js-at-scale-module-system-commonjs-require/)模块系统。

* * *

Lullabot Education 正在加强我们的 Node.js 培训。注册我们的[简讯](https://www.getdrip.com/forms/662782570/submissions/new)并了解即将推出的指南和教程——帮助塑造 Node.js 教育的未来。