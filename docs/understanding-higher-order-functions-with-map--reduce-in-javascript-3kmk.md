# 用 JavaScript 中的 map & reduce 理解高阶函数

> 原文：<https://dev.to/arghasoumak/understanding-higher-order-functions-with-map--reduce-in-javascript-3kmk>

# 高阶函数

[![](img/ad88fb548902815ea5a29ace14cf32bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dxVoGYVo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kbhojsffbglub5o38s92.png)

高阶函数是函数式编程中的一个热门话题。我们大多数人都在不知不觉中使用它。所以在这篇文章中，我将澄清所有关于高阶函数的困惑。

在 JavaScript 中，函数被视为对象。它们可以被赋给变量，作为参数传递，从函数返回。所以我们称它们为一等品。

因此，高阶函数是接收函数作为参数或返回函数作为输出的函数。

这意味着我们可以将一个函数作为参数传递给另一个函数。喜欢..

```
const foo = call => call();

const sayHi = () => "hi";

foo(sayHi) 
```

这里我们将 sayHi 函数作为参数传递给 foo 函数。这种方法也称为回调。

我们也可以将函数作为输出返回。喜欢..

```
const foo = () => () => "Hi";

foo()() 
```

这里我们从函数 foo 返回一个匿名函数。

现在我们已经了解了高阶函数，我们可以学习一些内置在 Javascript 中的高阶函数。

## 地图

该映射是高阶数组函数。它需要一个回调函数，这个函数将为数组中的每个元素调用。它将返回一个全新的数组。

映射函数的语法。

```
array.map((current, index, array) => {}) 
```

我们举个例子。

```
const arr = [1, 2, 3, 4, 5];

const newArr = arr.map(num => num * 2);

newArr  // 2, 4, 6, 8, 10 
```

在这里，我们为每个数字调用函数，并返回乘以 2 的数字。

注意:Map 不会改变实际的数组。相反，它用修改后的值创建一个新数组。

## 减少

Reduce 也是一个高阶函数。但是与 map 不同，它调用每个元素的回调并返回一个减少的值。

reduce 函数的语法。

```
array.reduce((accumulator, current, index, array) => {}, initialValue) 
```

我们举个例子。

```
const arr = [1, 2, 3, 4, 5];

const sum = arr.reduce((total, num) => total += num, 0);

sum  // 15 
```

这里，我们在一列数字中找到总和。Sum 是累加器。我们用 0 初始化它并返回总和。

我们可以利用累加器来计算数组中任何类型的值。

我们可以从列表中计算出最小数量。喜欢..

```
const are = [5,7,3,9,6,3];

const minimum = arr.reduce((min,num) => min < num ? min : num) 
```

因此，如果您想修改数组中的每个元素，请使用 map。当您希望将数组缩减为单个值时，请使用 reduce。

我希望你能从这篇文章中学到一些新东西。感谢阅读。