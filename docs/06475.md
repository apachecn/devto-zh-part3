# 新的 JavaScript 开发人员需要掌握什么🥋

> 原文：<https://dev.to/pan/what-new-javascript-developers-need-to-master-5dbe>

刚接触 JavaScript 的开发者需要掌握(1)作用域，(2)闭包和(3)异步函数。如果这三个概念被误解，可能会导致意外的行为和不稳定的代码。我曾经指导过或合作过的开发 C++或没有编程经验的开发人员都因为这些话题而丧失了生产力。无论您是在编写意大利面条式的 jQuery 代码、用 React.js 构建的前端，还是 Node.js 服务器，这些都是理解 JavaScript 的有用起点。

下面，我将通过一些例子来回顾这些概念，希望能澄清一些问题。

## 闭包和范围

### 1。关闭

在 JavaScript 中，闭包是通过声明函数来创建的。它是一种用特定的职责封装数据和逻辑的方法。

```
var jsClosure = function() {
  // Everything in here is inside a closure
} 
```

或者

```
function jsClosure() {
  // Everything in here is inside a closure
} 
```

### 2。范围

当一个闭包被创建时，它也伴随着一个作用域。通过作用域，特定于函数(闭包)的变量被封装。有两种类型的作用域——(a)在代码中的任何地方都可以访问的全局作用域，以及(b)只能在特定的闭包内访问的局部作用域。

当在错误的范围内访问某个变量时，会出现常见的错误，从而导致未定义的变量错误。

```
/* 
  Open a JavaScript console and execute each part of the code below in 
  your browser JavaScript console to see it in action (On Windows and 
  Linux: Ctrl + Shift + J. On Mac: Cmd + Option + J).
*/

var globalVariable = "Hi, I'm global and could be accessed anywhere"
function jsClosure() {
  var localVariable = "Hi, I'm local to jsClosure()"

  console.log(
    "Global variables are accessible in jsClosure() and everywhere",
    globalVariable
  )
}
console.log(
   "I am local to jsClosure() and will not be accessible outside of my
   scope. Executing this will actually not work at all and lead to
   an error", 
   localVariable
) 
```

更详细地说，闭包和作用域可以嵌套。

```
var globalVariable = "Hi, I'm global and can be accessed anywhere"
function jsClosure() {
  var localVariable = "Hi, I'm local to jsClosure()"

  console.log(
    "I am global so I am accessible in jsClosure() and everywhere",
    globalVariable
  )
  function jsInnerClosure() {
    var innerLocalVariable = "Hi, I'm local to jsInnerClosure()"

    console.log(
      "Globally accessible variables can be accessed here and
       everywhere",
       globalVariable
    )
    console.log(
      "Variables defined in the outer scope are also accessible
       in here. In this case localVariable", 
       localVariable
    )
   }
} 
```

## 异步行为

JavaScript 的一个主要优点是语言内置的异步功能。这允许我们编写“非阻塞”的代码，这对于用户体验是至关重要的，因为某些代码可能需要不确定的时间来执行。

下面是我们如何为我的 PAN 异步获取事务的示例🍳不阻止下一个`console.log`执行的令牌。

```
console.log("I am going to print 1st")
fetch("https://api-ropsten.etherscan.io/api?module=account&action=tokentx&contractaddress=0x3723268a20af802e37958ea2b37e2ba8ffc9cf17&page=1&offset=100&sort=asc")
  .then(function(response) {
    return response.json()
  })
  .then(function(panTransactions) {
    console.log(
      "I will not print 2nd because I am asynchronous and will print
       3rd after I am done processing",
      panTransactions
    )
  })
console.log("Although I am 3rd to execute, I will print 2nd") 
```

## 结论

如果你打算用 JavaScript 编程，理解闭包和作用域以及异步将有助于编写有效的代码。

## 相关内容

*   [范围](https://developer.mozilla.org/en-US/docs/Glossary/Scope)
*   [关闭](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)
*   [异步](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous)

> 如果你觉得这些内容很有用，并且想获得新内容的更新，请在 Twitter 上关注我。