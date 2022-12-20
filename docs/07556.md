# 学习折叠你的 JS 数组

> 原文：<https://dev.to/mebble/learn-to-fold-your-js-arrays-2o8p>

您可能遇到过这样的情况，您需要获取一组值并“收集”它们。我的意思是在数组上执行一些操作，这样我们最终可以只获得一个值。下面是几个例子。

你以前肯定对一组数字求和:

```
function sum(numbers) {
    let acc = 0;
    for (const num of numbers) {
        acc = add(acc, num);
    }
    return acc;
} 
```

Enter fullscreen mode Exit fullscreen mode

或者得到一组数字的乘积:

```
function prod(numbers) {
    let acc = 1;
    for (const num of numbers) {
        acc = mult(acc, num);
    }
    return acc;
} 
```

Enter fullscreen mode Exit fullscreen mode

或者在一组数字中找出最大的数字:

```
function maximum(numbers) {
    let acc = -Infinity;
    for (const num of numbers) {
        acc = max(acc, num);
    }
    return acc;
} 
```

Enter fullscreen mode Exit fullscreen mode

在每一个例子中，我们都取了一组东西，并执行了一些操作，将这些东西收集成一个单一的东西。

# 什么是折叠？

上述例子有几个共同点。它们都包含一些非常相似的部分:

*   存放最终结果的地方，通常称为累积或`acc`
*   累积的初始值(0、1 和`-Infinity`)
*   一个二元运算，结合了累加和我们当前正在处理的数组项(`add`、`mult`和`max`)

收集物品的过程显然遵循一种模式。我们目前正在重复大量的代码，所以如果我们能把它抽象成一个函数，我们会有更干净、更有表现力的代码。这种功能有一个名字，Fold ( [Wikipedia](https://en.wikipedia.org/wiki/Fold_(higher-order_function)) )。这个函数是函数式编程的基础之一。我们要做的是用 JS 自己实现 fold，为什么不呢？

## 几点观察

关于折叠，有三样东西是没有价值的。

二进制运算`add`、`mult`和`max`称为`reducers`。reducer 接受两个值——当前累积值和当前数组元素——并返回新的累积值。

初始值需要是相对于减速器的`identity`。这意味着当初始值和另一个值`x`一起传递给减速器时，输出总是`x`。例子:
`add(0, x) = x`
`mult(1, x) = x`
`max(-Infinity, x) = x`。
这里，`0`、`1`和`-Infinity`分别是相对于减速器`add`、`mult`和`max`的标识。我们需要它是一个`identity`，因为我们希望初始累积是“空的”。`0`为空 w.r.t .求和，`1`为空 w.r.t .乘积。

所有数组元素必须是相同的数据类型(比如说类型`A`)，但是累积的数据类型(比如说`B`)不必与数组元素的数据类型相同。例如，这段代码将一个数字数组折叠成一个字符串。

`// nothing` `const concatNum = (x, y) => x + y.toString(); // concatenates a string x and number y const numbers = [1, 2, 3, 4, 5]; // elements are of type number let acc = ''; // accumulation is of type string for (const num of numbers) { acc = concatNum(acc, num); } console.log(acc);`

注意减速器的接口必须是`reducer(acc: B, x: A): B`，在本例中是

```
concatNum(acc: string, x: number): string 
```

Enter fullscreen mode Exit fullscreen mode

# 创建一个折叠

说了很多。让我们最终完成折叠。fold 是一个高阶函数(对于 HOF intro，我强烈推荐[雄辩的 Javascript](http://eloquentjavascript.net/05_higher_order.html#h_xxCc98lOBK) )，它接受一个缩减器(一个函数)、一个累积初始值和一个数组(更正式的说法是一个列表，这是[JS 数组是什么](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#Description))。

我们先把 add/mult/max 减速器一般化，称之为`reducer`(惊喜！).我们称初始值为`init`。然后我们概括了一系列的事情。它可以是任何东西的数组，不仅仅是数字，所以我们称它为`xs`。我们现在已经定义了折叠！

```
const fold = (reducer, init, xs) => {
    let acc = init;
    for (const x of xs) {
        acc = reducer(acc, x);
    }
    return acc;
}; 
```

Enter fullscreen mode Exit fullscreen mode

你注意到这些论点的顺序了吗？我们首先传入`reducer`，接着传入`init`，然后传入`xs`，这是有原因的。这与[讨好](https://medium.com/javascript-scene/curry-and-function-composition-2c208d774983)有关，我们改天再谈。上面的例子现在看起来像这样，粗箭头样式:

```
const sum = xs => fold(add, 0, xs);
const prod = xs => fold(mult, 1, xs);
const maximum = xs => fold(max, -Infinity, xs); 
```

Enter fullscreen mode Exit fullscreen mode

好多了。

如果我们愿意的话，我们可以内联编写 reducer:

```
const sum = xs => fold((acc, x) => acc + x, 0, xs);
const prod = xs => fold((acc, x) => acc * x, 1, xs);
const maximum = xs => fold((acc, x) => (acc >= x) ? acc : x, -Infinity, xs); 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个交互式编辑器供您使用:

`// nothing` `const fold = (reducer, init, xs) => { let acc = init; for (const x of xs) { acc = reducer(acc, x); } return acc; }; const sum = xs => fold((acc, x) => acc + x, 0, xs); const prod = xs => fold((acc, x) => acc * x, 1, xs); const maximum = xs => fold((acc, x) => (acc >= x) ? acc : x, -Infinity, xs); const numbers = [3, 7, 1, 2, 5]; console.log('sum:', sum(numbers)); console.log('product:', prod(numbers)); console.log('maximum:', maximum(numbers));`

很简单，对吧？嗯，我们有点作弊。我们使用了 for 循环(更确切地说是 for...这在函数式编程世界中是一大禁忌。使用 for 循环进行数据转换意味着我们必须改变一些对象。这里，我们通过在循环中重新分配来改变`acc`。fold 的真正函数实现将使用递归并避免变异。我们将在另一篇文章中探讨这个问题。

### 几个有兴趣的注意事项

*   JS 已经有了一个 fold，这是一个在数组上可用方法。叫做[减](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)。所以我想你可以说我们自己重新实现折叠是毫无意义的🤷‍♂️(虽然我希望它能帮助一些 FP 新手)。
*   因为我们使用了 for...与普通的 for 循环不同，我们做的 fold 不仅仅作用于数组——它作用于任何[可迭代对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)。
*   一般来说，fold 应该可以处理任何可枚举的数据源，比如列表和树。
*   “收集”的概念不一定是组合数组元素，比如加法或乘法。它可能是关于“查找和替换”，像最大/最小缩减器，或者关于“顺序应用”，像管道函数的函数应用缩减器(如果你感兴趣的话，可以是)。应用程序是无止境的！

一个用一堆东西来返回一个东西的函数可能看起来有点琐碎，但是我们将在下一篇文章中通过实现许多 folds 来看看它实际上有多强大。我们将扁平化数组，管道函数和[希望]做更多的折叠。