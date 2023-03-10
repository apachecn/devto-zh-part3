# 2019 年声明 JS 变量

> 原文：<https://dev.to/samthor/declaring-js-variables-in-2019-4572>

TL；博士我的看法🤔💭:**默认使用`const`**，仅在需要时使用`let`。

## 历史

JavaScript 有三种*方法声明变量:

```
var x = 123;
let y = 456;
const z = 789; 
```

Enter fullscreen mode Exit fullscreen mode

第一个，`var`，来自 JavaScript 的原始版本。后两者在 2016 年左右广泛应用于各种浏览器。

## 默认为常数

如果你用`const`声明变量，它们就不能被改变(“变异”)。(如果变量指向一个*对象*，你可以改变这个对象。)比如:

```
const value = 123;
const object = {abc: 123};

object.abc++;   // ok ✅
value++;        // error ❌
object = null;  // error ❌ 
```

Enter fullscreen mode Exit fullscreen mode

在构建 JS 时，这是一种非常原始但有用的安全方法。如果一个变量不应该变异——它是一些复杂操作的结果——那么`const`意味着你不会意外地错误使用它。(这在与[严格模式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)结合使用时也很有用，在这种模式下，你不能只发明没有`var`、`let`或`const`的变量名。🔬

## 按需出租

如果你后来发现你需要一个变量来变异，你可以回到它的声明并把它标记为`let`。这让你的程序的读者对变量有更多的了解。

```
const counter = getTotalEvents();

// ...later, we decide to add something, but this will 💥
counter += otherEvents(); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们将`const counter`修改为`let counter`，读者会知道这不是最终值:它可能会在下面进一步变异。

## 明白了

*   函数参数总是可变的。

```
function foo(foobar) {
  ++foobar;
  return foobar;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   您可以并且应该在`for-of`或`for-in`循环中使用`const`(这并不总是显而易见的，因为似乎变量在每次迭代中都会发生变化)。🤔

```
for (const x in object) { ... }
for (const x of arrayOrIterable) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

## Var 被认为令人困惑

用`var`声明有些奇怪——变量声明被*提升到了函数的顶部，所以它总是可用的。这里有一个例子，但是它表明你可以只使用`let`来代替:*

[![let vs var](img/8da10fd8928b290848dcc84055a14f6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UP1jS_PN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f9aaw9v8ojbt59z20s8f.gif)

用`var`声明在某些场合可能会有用，但我相信它并没有遵循[的最小惊喜](https://www.google.com/search?q=principle+of+least+surprise)原则，你随时可以用`let`代替。

## 例外*

从技术上讲，函数声明是声明变量的另一种方式。

```
function foo() {
  // do stuff
}

// "foo" is now something we can pass around, e.g.:
setTimeout(foo, 1000); 
```

Enter fullscreen mode Exit fullscreen mode

这不是*确切地说*喜欢说`var foo = function() { ... }`，但它是相似的。主要的区别是声明本身被提升，而不仅仅是变量名。下面是实际操作:

```
foo();  // ok! ✅
bar();  // crashes: "bar" is a valid name, but contains undefined 😕

function foo() {}
var bar = function bar() {}; 
```

Enter fullscreen mode Exit fullscreen mode

函数声明*在 JS 中是一个有用的概念，因为它让我们在声明函数之前传递函数——允许循环🔄引用，比如将函数作为回调来传递。(如果你好奇，我会在[🗣️演讲中谈论它如何与 ES6 模块交互💬2017 年聚合物峰会上我给的](https://www.youtube.com/watch?v=fIP4pjAqCtQ&list=PL5vCN4IjFTd40C6QRiHfs6h4MOdjbRo3B&index=14&t=0s)。*

## 谢谢！

今天到此为止，只是简单的观点篇！

five