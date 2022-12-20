# 嘿，爱丽丝，JavaScript 的严格模式有什么大不了的？

> 原文：<https://dev.to/ashubhadoria7/hey-alice-what-s-the-big-deal-about-the-javascript-s-strict-mode-24bb>

在 Alice 开始回答 Bob 的问题之前，让我为这次对话做好准备。因为 JavaScript 可以在没有完全或充分理解的情况下使用，所以对语言的理解以及为什么某些东西会工作或为什么要使用它是我们无法理解的。假设 Bob 是一名开发人员，他想多做一点工作来理解他每天都在使用的东西，而不是有意识地思考。Alice 是一名善解人意的开发人员，她想帮助她的同事。

(也许在不同的环境中，鲍勃可能正在为一份全职工作面试爱丽丝。😉)

#### 嘿，爱丽丝，我的代码工作起来很有魅力，但是我被告知要使用`strict mode`，有什么好主意吗？

JavaScript `strict`模式基本上允许你把一个程序甚至一个函数放在一个严格的操作上下文中。在你问“这对我有什么好处？”，我也来回答一下。这将使调试变得更容易，它将使你不那么容易受到 JavaScript 语言的影响。如果您使用`strict`模式，许多在运行时(可能是在生产中)被发现的错误将会悄悄地失败。

#### 那么我如何使用*传说中的* `strict`模式呢？

哦！只需将字符串`"use strict";`放在程序文件的顶部。现在你的代码使用的是`strict`模式，许多在*非严格*模式下看起来很好的代码行将中断，迫使你重构你的程序，使之更易于调试。我们都让我们的生活变得更容易。

#### 这难道不意味着我将通过在我的整个程序文件上使用`strict`模式来破解很多老爷爷遗留的代码吗？

记得我告诉过你，你可以在一个功能上设置`strict`模式。所以如果你现在不想伤害爷爷的感情，考虑开始在你写的函数上使用`strict`模式。

```
// non-strict mode

function doSomethingCool() {

    "use strict";

    // some cool `strict` stuff
}

// non-strict mode 
```

#### 为什么`"use strict"`一定是字符串？

因为当这个*功能*被开发的时候，很多旧的浏览器不支持`strict`模式。因此，如果这样一个遗留浏览器遇到了`"use strict"`，它会将其视为一个简单的字符串文字，不做任何事情(在影响程序状态的上下文中)。

#### 太棒了，爱丽丝，你能通过代码示例演示一下`"use strict"`如何让我受益吗？

啊！我以为你不会问，让我想几个场景。

*   **严格模式不允许你在声明变量之前给它赋值**

```
"use strict";

myName = "Alice"; 
```

```
ReferenceError: assignment to undeclared variable myName script.js:3:1 
```

这防止污染全球范围。
看这个例子，

```
"use strict";

var doAwesomeStuff = false;

doawesomeStuff = true // OOPS messed up the camel casing!!!

if (doAwesomeStuff) {

    console.log("Beginning to do some awesome stuff");

    // Awesome stuff 
} 
```

```
ReferenceError: assignment to undeclared variable doawesomeStuff script.js:5:1 
```

如果没有`strict`模式，你将永远不会得到一个输入错误的错误(无声地失败),你也永远无法完成*的惊人之作*。多亏了`strict`模式，你现在可以修改你的错别字，继续做你注定要做的*精彩的事情*。

*   **严格模式禁止使用为将来使用而保留的 JavaScript 关键字**

```
// Non-strict mode
var let = 5;

console.log(let); 
```

```
5 
```

```
"use strict";

var let = 5;

console.log(let); 
```

```
SyntaxError: let is a reserved identifier script.js:3:4 
```

现在已经是 2019 年了，人们开始使用最新的 JavaScript 特性。其中之一就是`let`关键词。`let`关键字用于在 [ES6 (ECMAScript 2015)](https://www.ecma-international.org/ecma-262/6.0/) 中声明块范围的变量。将它用作标识符名称是不明智的。所以`strict` mode 会支持你的。

*   **严格模式让*可怕的* `eval()`更安全的**

`eval()`是 JavaScript 中的一个全局函数，将指定的字符串作为 JavaScript 代码进行求值并执行。不鼓励使用`eval`,主要是因为它可能会在运行时改变词法范围(如果这看起来像胡言乱语，现在就忽略它😄)

```
// Non-strict mode 

var b = 5;

eval("var b = 6");

console.log(b); 
```

```
6 
```

注意到`eval()`继续在它存在的范围内重新声明变量。轻率地使用`eval()`可能会导致范围污染，在更复杂的场景中会导致意想不到的结果和复杂的逻辑。

```
"use strict";

var b = 5;

eval("var b = 6");

console.log(b); 
```

```
5 
```

在`strict`模式中，变量声明发生在一个单独的`eval()`作用域中(简单地说，这个作用域跨越了传递给`eval()`的参数)，因此不会污染当前的作用域。

*   **严格模式不允许删除变量、函数定义和函数参数**

```
"use strict";

var a = 10;
delete a; 
```

```
SyntaxError: applying the 'delete' operator to an unqualified name is deprecated script.js:4:7 
```

```
"use strict";

function dontDelete() {
    // some stuff
}

delete dontDelete; 
```

```
SyntaxError: applying the 'delete' operator to an unqualified name is deprecated script.js:7:7 
```

```
"use strict";

function dontDelete(arg) {
    delete arg;
    // some stuff
} 
```

```
SyntaxError: applying the 'delete' operator to an unqualified name is deprecated script.js:4:11 
```

这些限制主要是为了确保我们不会让 JavaScript 引擎难以优化我们的代码，并使代码块的语义更容易描述。

#### 总结起来

JavaScript 的`strict`模式确保我们的代码更干净，并且在代码交付生产之前避免一些非常常见的语法错误。然而`strict`模式并不是解决所有问题的灵丹妙药，我们仍然需要做好*的编程*😅