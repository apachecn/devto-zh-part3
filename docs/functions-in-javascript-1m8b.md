# JavaScript 中的函数

> 原文：<https://dev.to/palaklive/functions-in-javascript-1m8b>

[![Functions in JavaScript](img/b69fe57524900bcda274080314f8538f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q2rxnLRA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m45orbic4gv13vvlabxm.jpg)

## 什么是函数？

函数基本上是程序的“构建模块”,或者我们可以说函数是一个过程，它接受一些称为`arguments`的输入，并产生一些称为`return value`的输出。它们允许代码被多次调用而不会重复。

看起来是这样的:

```
function showMessage() {
  alert('Hello everyone!');
}
showMessage() 
```

Enter fullscreen mode Exit fullscreen mode

#### 函数声明被吊起

函数声明被提升——整体移动到当前作用域的开头。这允许你引用后面声明的函数:

```
function foo() {
    bar();  // OK, bar is hoisted
    function bar() {
        console.log('bar executed successfully.')
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，虽然 var 声明也被提升(参见变量被提升)，但是它们执行的赋值没有:

```
function foo() {
    bar();  // Not OK, bar is still undefined
    var bar = function () {
        // Uncaught TypeError: bar is not a function
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 特殊变量自变量

可以用任意数量的参数调用 JavaScript 中的任何函数；语言永远不会抱怨。然而，它将通过特殊变量`arguments`使所有参数可用。参数看起来像一个数组，但是没有数组方法:

```
> function f() { return arguments }
> var args = f('a', 'b', 'c');
> args.length
3
> args[0]  // read element at index 0
'a' 
```

Enter fullscreen mode Exit fullscreen mode

#### 定义函数的方式:

*   纯函数
*   函数声明
*   函数表达式
*   本地功能
*   递归函数
*   回收
*   立即调用的函数表达式

#### 纯函数

*   给定相同的输入，将总是返回相同的输出。
*   不会产生副作用。

#### 功能声明:

首先是 function 关键字，然后是函数名，然后是括号中的参数列表(在上面的例子中为空)，最后是花括号中的函数代码，也称为“函数体”。

```
function add(param1, param2) {
    return param1 + param2;
} 
```

Enter fullscreen mode Exit fullscreen mode

前面的代码定义了一个函数 add，它有两个参数 param1 和 param2，并返回这两个参数的和。这是调用函数的方法:

```
> add(6, 1)
7
> add('a', 'b')
'ab' 
```

Enter fullscreen mode Exit fullscreen mode

#### 函数表达式

定义 add()的另一种方法是将函数表达式赋给变量 add:

```
var add = function (param1, param2) {
    return param1 + param2;
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 原生功能

“原型”属性被 JavaScript 本身的核心广泛使用。所有内置的构造函数都使用它并且`native prototypes`可以是`modified`。
*也就是说，如果我们给`String.prototype`添加一个方法，它对所有的字符串都可用:*

```
 "hello folks!".toUpperCase() // HELLO FOLKS!

"Hello Folks!".toLowerCase() // hello folks!

"hello folks!".toTitleCase()
// Uncaught TypeError: "hello folks!".toTitleCase is not a function

// Let's Define a native function
String.prototype.toTitleCase = function() {
  return this.replace(
            /\w\S*/g,
            function(txt) {
                return txt.charAt(0).toUpperCase() + txt.substr(1).toLowerCase();
            }
        );
};

"hello folks!".toTitleCase() 
```

Enter fullscreen mode Exit fullscreen mode

在开发过程中，我们可能会有新的内置方法的想法，我们可能会尝试将它们添加到本地原型中。但那一般都是`a bad idea`。

##### 重要:

原型是`global`，所以很容易产生冲突。如果两个库添加一个方法 String.prototype.toTitleCase，那么其中一个将会是另一个的`overwriting`。

因此，一般来说，修改原生原型被认为是一个坏主意。

#### 递归函数

递归是一种迭代操作的技术，它让一个函数反复调用自己，直到得到一个结果。

当您需要在一个循环中用不同的参数重复调用同一个函数时，最好使用递归。

```
var factorial = function(number) {
  var result = 1;
  var count;
  for (count = number; count > 1; count--) {
    result *= count;
  }
  return result;
};

console.log(factorial(5)); // 120

function factorial(x) {
    if(x === 0) return 1
    return x * factorial(x-1);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 回调

在 JavaScript 中，函数是一级对象。`First-class`像任何其他对象一样的方式(字符串、数组、数字等)。

因为函数是一级对象，所以我们可以将一个函数作为参数传递给另一个函数，然后执行传入的函数，甚至将它返回以便以后执行。这就是在 JavaScript 中使用回调函数的本质。

```
// classic example of callback functions:

var newbies = ["Amit", "Darshan", "Dharmesh", "Priya", "Shivangi"];

newbies.forEach(function (bee, index){
    console.log(index + 1 + ". " + bee);
});

// User define

function sandwich(param1, param2, callback) {
  alert('Started eating my sandwich.\n\nIt has: ' + param1 + ', ' + param2);
  callback();
}

sandwich('jam', 'cheese', function() {
  alert('Finished eating my sandwich.');
}); 
```

Enter fullscreen mode Exit fullscreen mode

同样，请注意我们将匿名函数(没有名称的函数)作为参数传递给 forEach 方法的方式。

到目前为止，我们已经将匿名函数作为参数传递给了其他函数或方法。在我们看更具体的例子和开始制作我们自己的回调函数之前，让我们现在了解回调是如何工作的。

回调函数是如何工作的？

我们可以像传递变量一样传递函数，在函数中返回它们，并在其他函数中使用它们。当我们将回调函数作为参数传递给另一个函数时，我们传递的只是函数定义。我们不执行参数中的函数。

请注意，回调函数不会立即执行。它在包含函数体内的某个指定点被“回调”(因此得名)。

稍后将在函数体内调用匿名函数。即使没有名字，它仍然可以被包含函数通过 arguments 对象访问。

**使用命名函数作为回调**

这里有一个简单的例子:

```
function greeting(name) {
  if(name) alert('Hello ' + name);
}

function processUserInput(callback) {
  var name = prompt('Please enter your name.');
  callback(name);
}

processUserInput(greeting); 
```

Enter fullscreen mode Exit fullscreen mode

**将参数传递给回调函数**

```
function createQuote(quote, callback){ 
  var myQuote = "Like I always say, " + quote;
  callback(myQuote); // 2
}
function logQuote(quote){
  console.log(quote);
}
createQuote("Be a voice not an echo!", logQuote); 
```

Enter fullscreen mode Exit fullscreen mode

**什么是“回调地狱”？**
使用回调的 JavaScript 很难直观正确。许多代码最终看起来是这样的:

```
const verifyUser = function(username, password, callback){
   dataBase.verifyUser(username, password, (error, userInfo) => {
       if (error) {
           callback(error)
       }else{
           dataBase.getRoles(username, (error, roles) => {
               if (error){
                   callback(error)
               }else {
                   dataBase.logAccess(username, (error) => {
                       if (error){
                           callback(error);
                       }else{
                           callback(null, userInfo, roles);
                       }
                   })
               }
           })
       }
   })
}; 
```

Enter fullscreen mode Exit fullscreen mode

如何修复回调地狱？

糟糕的编码实践导致了回调地狱。幸运的是，编写更好的代码并不难！

你只需要遵守规则:

**保持你的代码浅显:**
下面是一些混乱的浏览器 JavaScript，它使用浏览器请求向服务器发出 AJAX 请求:

```
var form = document.querySelector('form')
form.onsubmit = function (submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, function (err, response, body) {
    var statusMessage = document.querySelector('.status')
    if (err) return statusMessage.value = err
    statusMessage.value = body
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码有两个匿名函数。让我们给他们起个名字吧！

```
var form = document.querySelector('form')
form.onsubmit = function formSubmit (submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, function postResponse (err, response, body) {
    var statusMessage = document.querySelector('.status')
    if (err) return statusMessage.value = err
    statusMessage.value = body
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以将函数移到程序的顶层:

```
document.querySelector('form').onsubmit = formSubmit

function formSubmit (submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, postResponse)
}

function postResponse (err, response, body) {
  var statusMessage = document.querySelector('.status')
  if (err) return statusMessage.value = err
  statusMessage.value = body
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，这里的函数声明是在文件的底部定义的。这要感谢`function hoisting`。

**模块化**

这是最重要的部分:任何人都能够创建模块(也就是库)

让我们把上面的样板代码拿出来，通过把它分成几个文件，把它变成一个模块。我将展示一个适用于浏览器代码或服务器代码(或者适用于两者的代码)的模块模式

```
// formuploader.js
module.exports.submit = formSubmit

function formSubmit (submitEvent) {
  var name = document.querySelector('input').value
  request({
    uri: "http://example.com/upload",
    body: name,
    method: "POST"
  }, postResponse)
}

function postResponse (err, response, body) {
  var statusMessage = document.querySelector('.status')
  if (err) return statusMessage.value = err
  statusMessage.value = body
} 
```

Enter fullscreen mode Exit fullscreen mode

module.exports 位是 node.js 模块系统的一个示例，它在遵循 ES 模块结构的节点前端框架中工作。

```
// formuploader.js
var formUploader = require('formuploader')
document.querySelector('form').onsubmit = formUploader.submit 
```

Enter fullscreen mode Exit fullscreen mode

#### 立即调用函数表达式(IIFE)

IIFE(立即调用的函数表达式)是一个 JavaScript 函数，一旦定义就运行:

该函数成为立即执行的函数表达式。不能从表达式外部访问表达式中的变量。

```
(function () {
    var aName = "Barry";
})();
// Variable name is not accessible from the outside scope
aName // throws "Uncaught ReferenceError: aName is not defined" 
```

Enter fullscreen mode Exit fullscreen mode

将寿命赋给变量存储的是函数的返回值，而不是函数定义本身。

```
var result = (function () {
    var name = "Barry"; 
    return name; 
})(); 
// Immediately creates the output: 
result; // "Barry" 
```

Enter fullscreen mode Exit fullscreen mode

特别感谢:

*   [Mozilla 开发者网络](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
*   [回调地狱](http://callbackhell.com/)