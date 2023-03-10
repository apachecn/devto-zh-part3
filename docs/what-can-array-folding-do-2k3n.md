# 数组折叠能做什么？

> 原文：<https://dev.to/mebble/what-can-array-folding-do-2k3n>

这是“Folds”系列的第 2 部分，在这里我们将了解如何使用简单的 Fold 模式来执行各种数组处理任务。

## 那是什么来着？

在[之前的文章](https://dev.to/mebble/learn-to-fold-your-js-arrays-2o8p)中，我们研究了折叠是如何在引擎盖下工作的。让我们再来回顾一下:

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

它使用一个`for..of`循环来遍历列表`xs`，每次都减少列表，直到最后只有一个值。这种编程模式非常强大。当我第一次知道这个折叠的时候，我怀疑这样一个简单的操作怎么能做这么多。但事实证明，编程中的许多问题都是归约问题——我们有一个事物列表，我们希望从该列表中提取一条信息。

你们中的许多人可能熟悉 Python 的内置函数`sum`、`len`和`max`。所有这些功能本质上都是折叠的。我想看看仅使用上面的函数定义，我还能在 JavaScript 中实现多少折叠。这将真正展示这个看似简单的小功能可以完成的各种事情。下面是我们可以使用折叠创建的不同功能。

## 保持警惕

我想提一下，在下面显示的每个折叠中，有两个部分值得注意:

*   **缩减器:**我为每个折叠单独定义了缩减器，而不是内嵌的，就像`sum`折叠的`add`缩减器一样。减速器传递了两个参数，`acc`和`x`。`acc`的数据类型将是其初始值的数据类型。
*   **初始值:**注意每个折叠累积的初始值相对于缩减器是一个恒等式。例如，`0`是`sum`折叠中使用的初始值，因为它是`add`减速器下的标识。请记住，从缩减器的角度来看，累积的初始值基本上应该包含零信息。它应该是无效和无用的，就像`add`认为`0`没有信息一样。

## 看哪，褶皱

### **`sum`T4】**

`sum(xs: number[]): number`

```
const add = (acc, x) => acc + x;
const sum = xs => fold(add, 0, xs); 
```

Enter fullscreen mode Exit fullscreen mode

当被要求将一个值列表收集到一个值列表中时，`sum`可能是您想到的第一件事。

### **`len`T4】**

`len(xs: any[]): number`

```
const inc = (acc, x) => acc + 1;
const len = xs => fold(inc, 0, xs); 
```

Enter fullscreen mode Exit fullscreen mode

这是对广受欢迎的来自 Python 的`len`的模仿。在减速器中，我们忽略了每个元素`x`，只是增加了一个`1`来代替。

### **`product`T4】**

`product(xs: number[]): number`

```
const mult = (acc, x) => acc * x;
const product = xs => fold(mult, 1, xs); 
```

Enter fullscreen mode Exit fullscreen mode

一系列数字的乘积。在`xs`中即使只有一个`0`也会使这个折叠无效。

### **`join`T4】**

`join(xs: any[]): string`

```
const concat = (acc, x) => `${acc}${x}`;
const join = xs => fold(concat, '', xs); 
```

Enter fullscreen mode Exit fullscreen mode

这将连接一个字符串列表，或者任何东西的列表，真的！将`x`注入模板字符串会调用它的`.toString()`方法。所以我说宣言是`join(xs: any[]): string`，不够具体。我真正想要的是`xs`是`xs: A[]`类型，其中`A`是一种数据类型，当我们调用它的`.toString()`时，它返回一个格式良好的字符串。没有静态类型，我们无法在 JavaScript 中做到这一点。我们在其他语言中也看到了这个特性，比如 Haskell 中的 [Typeclasses 和 TypeScript](http://learnyouahaskell.com/types-and-typeclasses) 中的[接口。如果没有它，JS 会尝试用默认的方式 stringify `x`，这对于更复杂的对象来说可能不是很好。](https://www.typescriptlang.org/docs/handbook/interfaces.html)

### **`all`T4】**

`all(xs: boolean[]): boolean`

```
const and = (acc, x) => acc && x;
const all = xs => fold(and, true, xs); 
```

Enter fullscreen mode Exit fullscreen mode

我真的很喜欢`all`和`some`褶皱看起来有多干净。但有一个问题是，当结果变得明显时，它们不会跳出循环。`all([false, true, true, true])`将遍历整个列表，即使结果已经被第一个`false`知道。

### **`some`T4】**

`some(xs: boolean[]): boolean`

```
const or = (acc, x) => acc || x;
const some = xs => fold(or, false, xs); 
```

Enter fullscreen mode Exit fullscreen mode

### **`maximum`T4】**

`maximum(xs: number[]): number`

```
const max = (acc, x) => (x > acc) ? x : acc;
const maximum = xs => fold(max, -Infinity, xs); 
```

Enter fullscreen mode Exit fullscreen mode

`maximum`和`minimum`可以用于任何可排序数据类型的数组，比如 JavaScript 字符串。但是我们必须使用合适的初始值。我们在这里使用的`-Infinity`，只适用于数字数组。

### **`minimum`T4】**

`minimum(xs: number[]): number`

```
const min = (acc, x) => (x < acc) ? x : acc;
const minimum = xs => fold(min, Infinity, xs); 
```

Enter fullscreen mode Exit fullscreen mode

### **`flatten`T4】**

`flatten(xs: any[][]): any[]`

```
const concatArray = (acc, x) => [...acc, ...x];
const flatten = xs => fold(concatArray, [], xs); 
```

Enter fullscreen mode Exit fullscreen mode

这是我最喜欢的一个。这里发生了大量的数组复制。我们本可以使用`acc.push(...x)`改变`acc`并返回它以避免总是复制`acc`，但是你必须承认，spread 操作符看起来干净多了。这将一个数组展平一层，就像 Lodash 的 [_。压平](https://lodash.com/docs/4.17.11#flatten)。

### **`merge`T4】**

`merge(xs: object[]): object`

```
const combine = (acc, x) => ({ ...acc, ...x });
const merge = xs => fold(combine, {}, xs); 
```

Enter fullscreen mode Exit fullscreen mode

`merge`与`flatten`非常相似，除了它作用于对象。它的行为就像 JavaScript 的内置 [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 。

### **`reverse`T4】**

`reverse(xs: any[]): any[]`

```
const prepend = (acc, x) => [x, ...acc];
const reverse = xs => fold(prepend, [], xs); 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法是使用`acc.unshift(x)` ( [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift) )变异`acc`并返回它，而不是通过 spread 操作符复制它。

**警告:**这种弃牌有点奇怪。还记得我说过累加的初始值应该是一个恒等式吗？嗯，这里的这个，`[]`，不是。`prepend([], x)`将返回`[x]`。据维基百科上的[篇](https://en.wikipedia.org/wiki/Fold_(higher-order_function)#Special_folds_for_non-empty_lists)折:

> 人们常常想选择运算 f 的*恒等式*元素作为初始值 z。

没有提到对身份元素的严格要求。因此，在我们混乱的编程世界里，一些优雅的数学规则可能会被打破。或者我只是在某个地方做了一个 oopsie。

### **`pipe`T4】**

`pipe(xs: { (x: any): any }[]): (x: any): any`

```
const composeR = (acc, x) => {
    return m => x(acc(m));
};
const pipe = xs => fold(composeR, x => x, xs); 
```

Enter fullscreen mode Exit fullscreen mode

这个是我最喜欢的。我可能在这里破坏了管道函数的类型声明，所以请原谅我。我觉得有趣的是 acc 的初始值，`x => x`。它真正让我们明白了这样一个概念，初始值是关于缩减量的一个恒等式。至于减速器，就像数学上的[函数组成](https://en.wikipedia.org/wiki/Function_composition)，除了反过来。

管道接受一个一元函数列表，并返回一个按顺序运行它们的函数。每个函数的返回值作为参数传递给下一个函数。

### **`last`T4】**

```
const second = (acc, x) => x;
const last = xs => fold(second, null, xs); 
```

Enter fullscreen mode Exit fullscreen mode

我只是觉得把它放在最后比较合适。

## 不止一折

到目前为止，我们看到的所有例子都是 folds——它们接受一个事物列表，只返回一个事物。接下来的这些并不是完全意义上的 fold，但是我们仍然可以使用 fold 来实现它们。没错，`map`和`filter`都可以由折而成！

它们不仅仅需要一个`xs`参数；他们还需要一个功能`f`。所以缩减器必须被内联定义，这样我们就可以通过缩减器的闭包来捕获`f`。这些例子也打破了同一性规则(参见上面的`reverse`部分)。

### **`map`T4】**

```
const map = (f, xs) => fold((acc, x) => [...acc, f(x)], [], xs); 
```

Enter fullscreen mode Exit fullscreen mode

### **`filter`T4】**

```
const filter = (f, xs) => fold((acc, x) => {
    return f(x)
        ? [...acc, x]
        : acc;
}, [], xs); 
```

Enter fullscreen mode Exit fullscreen mode

在`map`和`filter`中，我们在 `xs`之前传入函数`f` *，使它们“迭代优先，数据最后”。这是为了让我们能够利用[curry](https://medium.com/javascript-scene/curry-and-function-composition-2c208d774983)的能力，使我们的代码更加模块化和可组合。*

同样，我们可以用`acc.push`来改变`acc`，但是这有什么好的呢？这将违背 FP 宣扬的不变性原则。我开玩笑的当然，这些都只是实验。在一个实际的软件中，我们真的不想让*在我们自己的 JS 实现中太过*功能化，因为 JS 并不是为它而优化的(除非我们完全知道我们在做什么)。为此，我们最好使用现有的库，如 lodash/fp 或 Ramda。

## 一个游乐场

上面的每一段代码都包含在下面的这个游戏中。我还放了一些例子来说明我们如何一起使用这些折叠。不过有一个小小的警告:它在手机屏幕上看起来非常混乱。

`const fold = (reducer, init, xs) => { let acc = init; for (const x of xs) { acc = reducer(acc, x); } return acc; }; // reducers const add = (acc, x) => acc + x; const inc = (acc, x) => acc + 1; const mult = (acc, x) => acc * x; const concat = (acc, x) => `${acc}${x}`; const and = (acc, x) => acc && x; const or = (acc, x) => acc || x; const max = (acc, x) => (x > acc) ? x : acc; const min = (acc, x) => (x < acc) ? x : acc; const concatArray = (acc, x) => [...acc, ...x]; const combine = (acc, x) => ({ ...acc, ...x }); const prepend = (acc, x) => [x, ...acc]; const composeR = (acc, x) => { return m => x(acc(m)); }; const second = (acc, x) => x; // folds const sum = xs => fold(add, 0, xs); const len = xs => fold(inc, 0, xs); const product = xs => fold(mult, 1, xs); const join = xs => fold(concat, '', xs); const all = xs => fold(and, true, xs); const some = xs => fold(or, false, xs); const maximum = xs => fold(max, -Infinity, xs); const minimum = xs => fold(min, Infinity, xs); const flatten = xs => fold(concatArray, [], xs); const merge = xs => fold(combine, {}, xs); const reverse = xs => fold(prepend, [], xs); const pipe = xs => fold(composeR, x => x, xs); const last = xs => fold(second, null, xs); // other things we could make through folding const map = (f, xs) => fold((acc, x) => [...acc, f(x)], [], xs); const filter = (f, xs) => fold((acc, x) => { return f(x) ? [...acc, x] : acc; }, [], xs); const A = [ [0, 1], [2, 3, 7, 8], [9, 13], [16] ]; // find the sum of each row of A b = map(sum, A); console.log('b:', b); // reverse each row of A and then flatten c = flatten(map(reverse, A)); console.log('c:', c); // get half of the absolute value of every number const nums = [3, -8, 6, 23, -100, 8, 1]; d = map(pipe([Math.abs, x => x / 2]), nums); console.log('d:', d); // filter out invalid words and make the remaining go UPPER!! const words = ['cat', 'sl2k3', 'dog', 'sn@k3', 'bird']; const validUpper = (ws) => { const validWords = filter(w => /^[a-z]+$/i.test(w), ws); const upper = map(x => x.toUpperCase() + '!!', validWords); return upper; }; e = validUpper(words); console.log('e:', e);`

就像我在之前的文章中所说的，我们实现折叠的方式是一种黑客行为。

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

我们使用了 for 循环并重新分配了`acc`变量，这是对不可变性原则的不尊重。我们将在下一篇文章中看到如何做到这一点。

本文的一些想法受到了以下内容的启发:

*   [一篇关于折叠的中篇文章](https://medium.com/@zaid.naom/exploring-folds-a-powerful-pattern-of-functional-programming-3036974205c8)
*   [Haskell 的摺叠部分](http://learnyouahaskell.com/higher-order-functions#folds)