# 数组方法:通向函数式编程的大门

> 原文：<https://dev.to/grodier/array-methods-the-gateway-to-functional-programming-f29>

得益于 React 和 Redux 等框架，函数式编程似乎正在 JavaScript 社区中流行起来。拥有更多可读和可测试的代码是显而易见的。但是像函子、奉承、高阶函数和单子这样的词可能会吓到想要加入的人。幸运的是，你不需要知道一切就可以开始！

作为一种编程语言，我最喜欢 JavaScript 的一点是它是多参数的。它支持通过原型继承和工厂函数使用面向对象的范例。这是大多数 JavaScript 程序员都熟悉的。然而，函数也是 JavaScript 中的一等公民。这意味着函数可以像任何其他对象一样工作。它们可以赋给变量，作为参数传递给函数，甚至作为函数的值返回。这很重要，因为这意味着 JavaScript 也支持函数式编程。

JavaScript 支持面向对象和函数式编程范例的最大好处是它们并不互相排斥。你可以根据你的目标来混合搭配。这也将允许你不必完全投入就可以涉足功能世界。通过关注数据操作和使用数组方法，您可以开发一个坚实的函数基础。

在进入数据操作和数组方法之前，我想提供一个函数式编程的简要定义，它来自 Eric Elliott 的关于编写软件的博客系列:

> 函数式编程是通过组合纯函数、避免共享状态、可变数据和副作用来构建软件的过程。函数式编程是声明性的而不是命令性的，应用程序状态流过纯函数。

该定义包含了函数式编程的几个关键主题，比如纯函数、不变性、声明式风格和组合，这些将在更深入地研究数据操作和数组方法时出现。

## 以操纵数据为起点

这可能过于简单，但是编程涉及到三件不同的事情:获取输入、操作数据和提交输出。输入可以是从数据库读取的结果、用户触发的事件或 API 调用。同样，发布输出可以是一个 API、一个新文件或操作 DOM。

函数式编程的一部分是消除函数的副作用。副作用是任何在它自身范围之外操纵某种状态或变量的事情。

```
var x;
function addToX() {
  return x + 2;
}

function addTwo(n) {
  console.log(n + 2):
  return n + 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

在函数 addToX 中有一个明显的副作用，因为 x 在函数范围之外被修改。但是，在 addTwo 中，console.log 也是一个副作用，因为在函数返回内容的范围之外发生了一个可观察到的变化(一些内容被打印到屏幕上)。事实上，任何调用有副作用的函数的函数也被称为有副作用。

副作用会使发布数据很难从功能角度进行推理。然而，操纵数据不应该有任何副作用。你得到一个输入，你用这个输入做一些事情，然后你返回一个输出。给定相同的输入集，应该总是产生相同的输出。如果你能做到这一点，并且不会产生任何副作用，那么你的数据操作函数就是纯函数，你将会发现函数式编程的另一个主要支柱！

## 用数组方法操作数据

操纵数据通常涉及到迭代某些东西、修改数据、过滤掉不必要的数据或者将数据转换成不同的形状。很多时候，这是通过如下所示的 for 循环实现的。

```
// transform each item in an array
var newArray = [];
for (var i = 0; i < oldArray.length; i++) {
  var newValue = oldArray[i] + 2;
  newArray.push(newValue);
}

...

// filter out select values
var newArray = [];
for(var i = 0; i < oldArray.length; i++) {
  if (oldArray[i] % 2 === 0) {
    newArray.push(oldArray[i])
  }
}

...

// transform data from array to string
var myCatString = "";
for(var i = 0; i < oldArray.length; i++) {
  var seperator = myCatString ? "," : "";
  myCatString += `Cat ${oldArray[i]}${seperator}`;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的 for 循环只是迭代一组数据时可以做什么的几个小例子。然而，有一个问题。如果我没有在每个代码块之前留下注释，您就必须通读 for 循环的每一行才能理解它在做什么。上面的例子可能很容易理解，但是你经常会发现不同的方法被组合在一起。甚至可能在充满条件的 for 循环中有 for 循环。如果这听起来令人困惑，那是因为它确实如此，并且试图回过头来解释这段代码在做什么可能会很困难。

这还不是唯一的问题。上面的例子很小心，没有修改原始数组。但是，在给定的 for 循环中，没有任何承诺是真的。我读过许多 for 循环，它们在自己的作用域之外修改数据。这可能会导致另一组难以追踪的错误。

幸运的是，您可以通过使用数组方法来解决这些问题！

```
var newArray = oldArray.map(item => item + 2);

...

var newArray = oldArray.filter(item => item % 2 === 0);

...

var myCatString = oldArray.reduce((newStr, item) => {
  var seperator = newStr ? "," : "";
  return `${newStr}${seperator}`;
}, "") 
```

Enter fullscreen mode Exit fullscreen mode

以下每个示例都与上面的 for 循环相同。然而，通过使用 map、filter 和 reduce，我清楚了迭代的意图。我可以很快看出一个循环试图实现什么，而不必通读每一行。它们映射到某个值来转换它，过滤到一个较小的列表，或者缩小到另一个对象形状。这些数组方法被称为**声明性的**，因为它们描述了*它们正在做什么*(不需要控制流)。这与命令式风格形成对比，命令式风格更加程序化，描述*如何做事情。*

另一个好处是这些数组方法将返回一个新对象(通常是一个新数组)。在所有的例子中，我都没有修改任何预先存在的数据。因此，我尊重**不变性**的功能概念。不变性意味着一旦对象被创建，就不能以任何方式修改它。通过保持你的结构不变，你可以帮助确保你的函数保持纯净，并且你不会引入任何副作用。

Map、filter 和 reduce 不是唯一可以使用的数组方法。还有很多其他的你也可以申请。请务必查看[文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#Methods)以了解更多信息，并查看浏览器对各种方法的支持。

## 点自由样式、闭包和数组方法

需要注意的一点是，每个数组方法都接受一个函数作为参数。这是作为一个一等公民的职能的一个明显的示范。因此，让我们重写地图迭代的功能，以使用一个可重用的函数。

```
function addTwo(n) {
  return n + 2;
}

oldArray.map(n => addTwo(n)); 
```

Enter fullscreen mode Exit fullscreen mode

在函数式编程中，你会经常发现一种叫做**无点**风格的东西。当访问一个对象的属性时，点自由实际上并不是指点操作符，而是指函数的参数，并尽可能不写它们。如果一个函数是另一个函数的参数，并且参数匹配(在数量和类型上)，则不需要传入参数。在前面的示例中，addTwo 和传入 map 的匿名函数具有相同的参数。在这种情况下，您只需要传入不带参数的 addTwo。不需要匿名函数。

```
function addTwo(n) {
  return n + 2;
}

oldArray.map(addTwo); 
```

Enter fullscreen mode Exit fullscreen mode

通过利用无指针风格，你可以拥有更加简洁和声明性的代码。但是，在参数不匹配的情况下，你能做什么呢？假设您仍然想要添加两个，但是您只有一个接受两个参数的 add 函数。由于参数列表不匹配，您不能使用自由点样式。

```
function add(n, m) {
  return n + m;
}

oldArray.map(n => add(n, 2)); 
```

Enter fullscreen mode Exit fullscreen mode

现在你可能会看着它想，有没有一种方法可以让我事先应用这两个，这样我就可以使用无点风格了？这就是高阶函数结合闭包发挥作用的地方。**高阶函数**是接受或返回另一个函数的任何函数。

所以，我们的目标是创建一个新函数，它接受一个函数和一些参数，然后返回一个新函数，这些参数被部分应用。

```
function partiallyApply(fn, ...firstArgs) {
  return function(...remainingArgs) {
    return fn(...firstArgs, ...remainingArgs);
  };
}

var partialAdd = partiallyApply(add, 2);
oldArray.map(partialAdd); 
```

Enter fullscreen mode Exit fullscreen mode

函数 partiallyApply 接受一个函数和一个初始参数列表，并返回一个将接受任何剩余参数的新函数。初始函数和 firstArgs 被保存，因为它们被返回函数的词法范围所封闭。闭包和作用域的内部工作方式值得一整篇文章来讨论。

然后可以使用 partiallyApply 函数创建一个新的 partialAdd 函数，该函数已经应用了一个参数。结果，你现在能够排列我们的参数并且使用一个点自由样式！

partiallyApply 函数，顾名思义，就是一个**部分应用**的例子。大多数函数库已经为您实现了这一点。非常相似和相关的是**阿谀奉承**。currying 和 partial application 都接受一个函数，并创建一个更具体的函数来使用。当部分应用程序接受一个函数并返回一个参数数量减少的新函数时，currying 将创建一个新函数链，每个函数接受一个参数。Currying 也可以用来创建一个更具体的功能，以类似的方式利用点自由风格。下面使用了一个 currying 函数，就像在函数库中看到的那样。

```
var add2 = _.curry(add)(2);
oldArray.map(add2); 
```

Enter fullscreen mode Exit fullscreen mode

## 链接和排版

map(以及其他一些返回新数组的数组方法)的一个有趣的副作用是，您可以将多个数组方法链接在一起。

```
[0, 1, 2, 3, 4].filter(isOdd).map(multiplyByTwo);
// [2, 6] 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，filter 将遍历初始数组中的每一项，如果该项满足传递给 filter 的函数中的条件，则将它添加到一个新数组中。然后，将对从 filter 返回的新数组调用 map 函数，遍历这些项中的每一项，并执行传递给 map 的函数中描述的操作。map 的结果将是返回另一个新数组。初始数组永远不会被修改。

知道您可以将事物链接在一起，您可能会想到您可以有多个映射，每个映射以某种方式转换数组中的数据。这看起来甚至是声明性的:

```
oldArray.map(addTwo).map(multiplyByThree); 
```

Enter fullscreen mode Exit fullscreen mode

但是，这里有一个问题。当只需要一次迭代时，您现在正在进行多次迭代。如果您可以在同一个循环中应用两种映射转换，那不是很好吗？你可以的！将两个函数组合在一起很容易。

一个函数只是一个更大的程序的组成部分。通常你会看到一个函数的输出变成了另一个函数的输入。在这种情况下，您可以创建一个新函数，它是其他两个函数的组合。

```
function addTwo(x) {
  return x + 2;
}

function mutliplyByThree(x) {
  return x * 3;
}

var addTwoMultiplyByThree = _.compose(
  multiplyByThree,
  addTwo
);

var num = addTwoMultiplyByThree(4);
// num == 18 
```

Enter fullscreen mode Exit fullscreen mode

compose 函数接受两个或多个函数，并返回一个新函数。在上面的例子中，当调用新函数 addTwoMultiplyByThree 时，它将其输入发送给 addTwo 函数。addTwo 的输出成为 multiplyByThree 的输入，以此类推。你可以把构图想象成这样:

```
multiplyByThree(addTwo(4))); 
```

Enter fullscreen mode Exit fullscreen mode

由于这种构成，可以重写双映射函数以使用一次迭代来代替:

```
var addTwoMultiplyByThree = _.compose(
  multiplyByThree,
  addTwo
);

oldArray.map(addTwoMultiplyByThree); 
```

Enter fullscreen mode Exit fullscreen mode

随着您对函数组合的熟悉，您将了解到它是任何函数式程序的基础。你将把小的可重用的功能片段组合成更大的片段。这些较大的作品也可以与其他较大的作品组合在一起。在你意识到之前，你已经有了一个完整的应用程序来以不同的方式操作数据。

## 从小开始学大

通过使用数组方法，您将开始学习函数式编程必须提供的许多东西，包括纯函数、不变性、组合、声明式风格，甚至无指针风格、curry、部分应用程序和高阶函数。这是在没有引用任何大术语如函子或单子的情况下完成的。尽管信不信由你，你已经从头到尾都在使用仿函数了(尽管现在我会让你自己去学习)。

目标不是一次拿起所有东西。相反，我们应该在操作数据时开始使用数组方法，看看如何从中学习函数概念。有些人可能认为数组方法并不遵循纯粹的函数式风格，但是通过从小处着手，现在就使用它们，作为开发人员，您可以获得很大的收益。希望当你看到改进时，你可以把它作为学习更多关于函数式编程以及如何把它应用到你的代码中的一个途径。