# 如何解开这个谜团

> 原文：<https://dev.to/melkornemesis/how-to-curry-this--uncurry-this-4l9f>

我的一个朋友给我发了一段代码，问我是否能帮他看看到底发生了什么。他知道他可以用它来做什么，但很好奇(每个开发人员都应该这样)理解它背后的魔力是否会给他带来许多如何编写代码的新选择。

这是一段代码:

```
const uncurryThis = Function.bind.bind(Function.prototype.call); 
```

你有没有发现自己在浏览一个库的源代码时，偶然发现一段代码使用了`bind()`、`call()`、`apply`甚至它们的组合，但你却直接跳到下一行，因为这显然是某种黑魔法？

好吧，让我们深潜。

# 上下文、范围、执行上下文

在这篇文章中，我们将会谈论很多关于上下文的内容，所以让我们从一开始就弄清楚它是什么，这样我们就不会混淆了。

在很多情况下，当谈到理解什么是**上下文**和**范围**时，会有很多困惑。每个函数都有关联的范围和上下文，但是**它们不一样**！一些开发人员倾向于错误地描述其中一个。

## 范围

**作用域是基于函数的**，与变量的可见性有关。当你在一个函数中声明一个变量时，这个变量是这个函数的私有变量。如果你嵌套了函数定义，**每个嵌套的函数都可以看到它在其中被创建的所有父函数的变量**。但是！父函数看不到在其子函数中声明的变量。

```
// ↖ = parent scope
// ↖↖ = grand parent scope
// ...

const num_global = 10;

function foo() {
  // scope has access to:
  // num_1, ↖ num_global
  const num_1 = 1;

  function bar() {
    // scope has access to:
    // num_2, ↖ num_1, ↖↖ num_global
    const num_2 = 2;

    function baz() {
      // scope has access to:
      // num_3, ↖ num_2, ↖↖ num_1, ↖↖↖ num_global
      const num_3 = 3;
      return num_3 + num_2 + num_1 + num_global;
    }

    return baz();
  }

  return bar();
}

console.log(foo()); // 16 
```

## 上下文

**上下文是基于对象的**，与函数体内`this`的值有关。`This`是对执行该函数的对象的引用。你也可以把上下文看作是**它基本上告诉你在函数内部的`this`** 上你可以访问什么方法和属性。

考虑这些函数:

```
function sayHi() {
  return `Hi ${this.name}`;
}

function getContext() {
  return this;
} 
```

### 场景一:

```
const person_1 = {
  name: "Janet",
  sayHi,
  getContext,
  foo() {
    return "foo";
  }
};

console.log(person_1.sayHi()); // "Hi Janet"
console.log(person_1.getContext()); // "{name: "Janet", sayHi: ƒ, getContext: ƒ, foo: ƒ}" 
```

我们已经创建了一个对象`person_1`，并为其分配了`sayHi`和`getContext`功能。我们还在这个对象上创建了另一个方法`foo`。

换句话说，`person_1`是这些函数的`this`上下文。

### 场景二:

```
const person_2 = {
  name: "Josh",
  sayHi,
  getContext,
  bar() {
    return "bar";
  }
};

console.log(person_2.sayHi()); // "Hi Josh"
console.log(person_2.getContext()); // "{name: "Josh", sayHi: ƒ, getContext: ƒ, bar: ƒ}" 
```

我们已经创建了一个对象`person_2`，并为其分配了`sayHi`和`getContext`功能。我们还在这个对象上创建了另一个方法`bar`。

换句话说，`person_2`是这些函数的`this`上下文。

#### 区别

你可以看到我们在`person_1`和`person_2`对象上都调用了`getContext()`函数，但是结果是不同的。在场景 1 中，我们得到额外的功能`foo()`，在场景 2 中，我们得到额外的功能`bar()`。这是因为每个函数都有不同的上下文，也就是说，它们可以访问不同的方法。

### 未绑定函数

当函数未绑定(没有上下文)时，`this`指的是全局对象。但是，如果在严格模式下执行该功能，`this`将默认为`undefined`。

```
function testUnboundContext() {
    return this;
}

testUnboundContext(); // Window object in browser / Global object in Node.js

// -- versus

function testUnboundContextStrictMode() {
    "use strict";
    return this;
}

testUnboundContextStrictMode(); // undefined 
```

## 执行上下文

这大概就是困惑的来源。

> 执行上下文(EC)被定义为 JavaScript 代码执行的环境。我所说的环境是指 JavaScript 代码可以访问的变量、对象和函数的值，构成了它的环境。
> 
> -<cite>[https://hacker noon . com/execution-context-in-JavaScript-319 DD 72 e8e 2c](https://hackernoon.com/execution-context-in-javascript-319dd72e8e2c)</cite>

**执行上下文**不仅指`this`的值，还指范围、闭包、...这个术语是由 ECMAScript 规范定义的，所以我们必须忍受它。

# 调用、应用、绑定

现在事情变得更有趣了。

## 调用不同上下文的函数

`call`和`apply`方法都允许您在任何期望的上下文中调用函数。两个函数都期望上下文作为它们的第一个参数。

`call`期望函数参数被显式列出，而`apply`期望参数作为数组传递。

考虑:

```
function sayHiExtended(greeting = "Hi", sign = "!") {
  return `${greeting}  ${this.name}${sign}`;
} 
```

### 谓

```
console.log(sayHiExtended.call({ name: 'Greg'}, "Hello", "!!!")) // Hello Greg!!! 
```

请注意，我们已经显式传递了函数参数。

### 适用

```
console.log(sayHiExtended.apply({ name: 'Greg'}, ["Hello", "!!!"])) // Hello Greg!!! 
```

注意，我们将函数参数作为数组传递。

## 将函数绑定到不同的上下文

另一方面,`bind`不立即调用具有新上下文的函数，而是创建绑定到给定上下文的新函数。

```
const sayHiRobert = sayHiExtended.bind({ name: "Robert" });
console.log(sayHiRobert("Howdy", "!?")); // Howdy Robert!? 
```

您也可以绑定参数。

```
const sayHiRobertComplete = sayHiExtended.bind(
  { name: "Robert" },
  "Hiii",
  "!!"
);
console.log(sayHiRobertComplete()); // Hiii Robert! 
```

如果你做了`console.dir(sayHiRobertComplete)`，你会得到:

```
console.dir(sayHiRobertComplete);
// output
ƒ bound sayHiExtended()
    name: "bound sayHiExtended"
    [[TargetFunction]]: ƒ sayHiExtended(greeting = "Hi", sign = "!")
    [[BoundThis]]: Object
        name: "Robert"
    [[BoundArgs]]: Array(2)
                0: "Hiii"
                1: "!!" 
```

你得到一个*外来对象*，它包装了另一个函数对象。你可以在 ECMAScript 官方文档[这里](https://www.ecma-international.org/ecma-262/6.0/#sec-bound-function-exotic-objects)阅读更多关于*绑定函数外来对象*。

# 用法

很好，你们中的一些人学到了新的东西，一些人只经历了你已经知道的东西——但是熟能生巧。

现在，在我们回到最初的问题之前，那就是:

```
const uncurryThis = Function.bind.bind(Function.prototype.call); 
```

让我向你提出一个问题，并用我们新获得的知识逐步创造一个解决方案。

考虑一组名字:

```
const names = ["Jenna", "Peter", "John"]; 
```

现在让我们假设您想要映射数组，并使所有的名称都是大写的。

你可以试着这样做:

```
const namesUppercased = names.map(String.prototype.toUpperCase); // Uncaught TypeError: String.prototype.toUpperCase called on null or undefined 
```

但是这个**不行**。这是为什么呢？这是因为`toUpperCase`方法被设计成在 string 上被调用。`toUpperCase`本身不期望任何参数。

所以你需要这样做:

```
const namesUpperCased_ok_1 = names.map(s => s.toUpperCase());
console.log(namesUpperCased_ok_1); // ['JENNA', 'PETER', 'JOHN'] 
```

## 提议

所以与其做`names.map(s => s.toUpperCase())`倒不如说这个`names.map(uppercase)`更好。

换句话说，我们需要创建一个函数，它接受一个字符串作为参数，并返回该字符串的大写版本。你可以说我们需要*解开* `this`并明确地将其作为一个参数传递。所以这是我们的目标:

```
console.log(uppercase("John")); // John
console.log(names.map(uppercase)); // ['JENNA', 'PETER', 'JOHN'] 
```

## 解

让我告诉你，我们如何才能实现这样的事情。

```
const uppercase = Function.prototype.call.bind(String.prototype.toUpperCase);
console.log(names.map(uppercase)); // ['JENNA', 'PETER', 'JOHN'] 
```

刚刚发生了什么事？让我们看看`console.dir(uppercase)`能透露些什么。

```
console.dir(uppercase);
// output:
ƒ bound call()
    name: "bound call"
    [[TargetFunction]]: ƒ call()
    [[BoundThis]]: ƒ toUpperCase()
    [[BoundArgs]]: Array(0) 
```

我们得到了一个`call`函数，但是它被绑定到了`String.prototype.toUpperCase`。所以现在当我们调用`uppercase`时，我们基本上是在`String.prototype.toUpperCase`上调用`call`函数，并给它一个字符串的上下文！

```
uppercase == String.prototype.toUpperCase.call
uppercase("John") == String.prototype.toUpperCase.call("John") 
```

## 助手

这很好，但是如果有一种方法可以创建一个助手，比如说`uncurryThis`，它可以接受一个函数并且*不受*和`this`的约束，就像在`uppercase`的例子中一样，那会怎么样呢？

没问题！

```
const uncurryThis = Function.bind.bind(Function.prototype.call); 
```

好吧，现在发生了什么？让我们来看看`console.dir(uncurryThis)` :

```
console.dir(uncurryThis);
// output:
ƒ bound bind()
    name: "bound bind"
    [[TargetFunction]]: ƒ bind()
    [[BoundThis]]: ƒ call()
    [[BoundArgs]]: Array(0) 
```

我们得到了一个`bind`函数，但是使用了`call`函数作为它的上下文。所以当我们调用`uncurryThis`时，我们基本上是在为`call`函数提供上下文。

我们现在能做的:

```
const uppercase = uncurryThis(String.prototype.toUpperCase); 
```

基本上是:

```
const set_call_context_with_bind = Function.bind.bind(Function.prototype.call)
const uppercase = set_call_context_with_bind(String.prototype.toUpperCase); 
```

如果你知道 do `console.dir(uppercase)`，你可以看到我们最终得到了与在**解**部分:
中相同的输出

```
console.dir(uppercase);
// output:
ƒ bound call()
    name: "bound call"
    [[TargetFunction]]: ƒ call()
    [[BoundThis]]: ƒ toUpperCase()
    [[BoundArgs]]: Array(0) 
```

viola，我们现在有了一个工具来解除对`this`的绑定，并显式地将其作为参数传递:

```
const uncurryThis = Function.bind.bind(Function.prototype.call);
const uppercase = uncurryThis(String.prototype.toUpperCase);
const lowercase = uncurryThis(String.prototype.toLowerCase);
const has = uncurryThis(Object.prototype.hasOwnProperty);

console.log(uppercase('new york')); // NEW YORK
console.log(uppercase('LONDON')); // london
console.log(has({foo: 'bar'}, 'foo')); // true
console.log(has({foo: 'bar'}, 'qaz')); // false 
```

# 我们完成了

谢谢你陪我到最后。我希望你已经学到了一些新的东西，也许这能帮助你理解一点`call`、`apply`和`bind`背后的魔力。

# 奖金

无论谁可能感兴趣，这里有一个不使用`bind`的`curryThis`版本:

```
function uncurryThis(f) {
  return function() {
    return f.call.apply(f, arguments);
  };
} 
```