# 如何在 JavaScript 中检查 NaN

> 原文：<https://dev.to/samanthaming/better-nan-check-with-es6-number-isnan-19g0>

JS 中的`isNaN`检查总是有它的问题——对于一个实际上不是数字的值，它返回 true😱。为什么？因为它首先将值强制为一个数字，这可能会错误地导致一个`NaN`。ES6 来救援了！`Number.isNaN`不会强行做转换。这意味着只有 number 类型的值将返回 true。这么👏

```
// What?? 😱
isNaN('string') // true

// Better ✅
Number.isNaN('string') // false 
```

## 我糊涂了...

> 等等，NaN 检查不是应该对字符串返回 true，对数字返回 false 吗？NaN 的意思是“不是一个数”，所以它不应该是检查“不是一个数”的东西吗？我很困惑

☝️如果你是这些人中的一员。别担心，我也是！让我试着为你解开这个谜团。

## 数.伊斯南 vs 伊斯南

让我们试着去理解这两种方法在做什么。

### isNaN

> 如果参数强制为 NaN，则返回 true，否则返回 false。

*[标准 ECMA-262: 5.1 版](https://www.ecma-international.org/ecma-262/5.1/#sec-15.1.2.4)*

所以这个方法将首先使用例如`Number(argument)`来强制参数，如果它的值为`NaN`，它将返回 true。

### 编号

> 确定参数是否为 NaN，其类型是否为 Number。

*[标准 ECMA-262:第六版](https://www.ecma-international.org/ecma-262/6.0/#sec-number.isnan)*

这里没有强迫。它做两件事:

*   首先，它使用例如`typeof (argument)`来检查类型，如果它的计算结果不是`number`，它将返回 false。
*   其次，它将检查参数是否有值`NaN`，如果有，它将返回 true。

### 总结

非常清楚的是，这两种方法的区别在于在确定参数是否有值`NaN`之前，`Number.isNaN`不会强制或强行将参数转换为数字。

## 理解`NaN`

好吧，让我们继续了解`NaN`是什么。它代表“不是一个数字”。但我觉得这才是问题的源头。如果你从字面上考虑它，你可能会把它解释为任何“不是数字”的东西，那么它一定是`NaN`。这是不正确的。它不是数字的反义词。以一根“绳子”为例。当然，它“不是一个数字”,但它不等于`NaN`。所以“弦”不是`NaN`🙅‍♀️

> 将 NaN 视为“无效的数字”、“失败的数字”，甚至“错误的数字”，比将其视为“不是数字”要准确得多。

*[凯尔·辛普森，你不知道 JS](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch2.md#the-not-number-number)T3】*

所以`NaN`是一个无效数字。当你试图对不是数字的值做一些数学运算时，你得到了`NaN`。

```
const invalid = 100 // "string";

console.log(invalid); // NaN 
```

### 了解楠的小技巧

帮助我理解这一点的另一种方式是把`NaN`看作它自己的价值。就像`200`是一个值一样，`NaN`也是它自己的值。不要用它的字面意义来称呼它，“不是一个数字”，只要把它读成一个词， **nan** 。这样你的大脑就不会认为它“不是一个数字”。

好了，向前看，我们将不再把`NaN`称为“不是一个数字”，它只是另一个值，这个值叫做`NaN`。相信我，当你这样做的时候，会有助于缓解很多困惑🙆‍♀️.

## 问题检查`NaN`

那么为什么我们需要一个方法来检查`NaN`。为什么这个方法`isNaN`会存在？它的存在是因为我们不容易检查一个值是否是`NaN`。那是因为`NaN`不等于它本身。所以我们不能简单地将一个论点与`NaN`相比较，来检查它是否相等或相同。迷茫？让我们走一遍。

比如说，我们需要检查一个值是否等于`100`。那很简单，我们可以这样和它本身比较:

```
const value = 100;

value === 100; // true 
```

好吧，我们用`NaN`试试这个逻辑。

```
const value = NaN;

value === NaN; // false
value == NaN; // false 
```

☝️It 不工作😫。`NaN`是一个非常特殊的值，它从不等于自身。而且因为这个问题。JavaScript 创建了全局实用程序`isNaN`来帮助我们检查一个值是否等于`NaN`。

## 问题与`isNaN`

因为没有简单的方法来检查一个值是否等于`NaN`,因为它不等于自身。全球实用程序`isNaN`就是为了解决这个难题而创建的。

```
const value = NaN;

isNaN(value); // true ✅ 
```

这太棒了。但是这个工具有一个问题😑

```
const value = 'string';

isNaN(value); // true 😱 
```

你看出问题了吗？让我们用语言来解释这个。`string`等于`NaN`吗？记住，不要把 NaN 想成“不是一个数”，它只是一个叫 NaN 的值。答案是否定的！`string`不等于`NaN`。让我往下钻:

```
"string" === NaN; // false
"string" == NaN; // false 
```

### `isNaN`问题的来源

让我们回到我们对`isNaN`的定义

> 如果参数强制为 NaN，则返回 true，否则返回 false。

见“要挟”二字。这就是问题所在！它首先强制值。换句话说，它试图将值转换成数字类型。让我们看看当我们这样做时会发生什么。

```
const value = 'string';

Number(value); // NaN 
```

☝️，你看到了吗！当您尝试将字符串类型转换为数字类型时，它会返回`NaN`。而值`NaN`有没有`NaN`的值？答案是肯定的。所以才要回归`true`。但是！这不是我们想要的。这是一个假阳性。为了纠正这一点，我们不希望发生任何胁迫！

## 问题用`Number.isNaN`解决

所以才推出了`Number.isNaN`方法！它解决了`isNaN`的误报问题。`Number.isNaN`不做任何强制。换句话说，它不会尝试将参数的类型转换为数字类型。

```
const value = 'string';

isNaN(value) // true ❌
Number.isNaN(value) // false ✅ 
```

记住这些方法意图的问题:“值是否等于`NaN`”。不是问值是不是“不是数字”。抱歉，我重复了。但这是人们很多困惑的来源，所以我真的想深入这个概念。希望现在一切都清楚了😆

## 演示`Number.isNaN` vs `isNaN`

这差不多是这篇文章的结尾。但是我想再举几个例子，让🥅.真正理解这个概念

### 举例:数字

该值是否等于`NaN`？答案是否定的。所以结果应该是`false`。记住它不是问值是否等于“不是一个数”。

```
const number = 100;

isNaN(number) // false ✅
Number.isNaN(number) // false ✅ 
```

### 举例:南

该值是否等于`NaN`？答案是肯定的。所以结果应该是`true`。

```
const nan = NaN;

isNaN(number) // true ✅
Number.isNaN(number) // true ✅ 
```

### 举例:字符串

始终使用`Number.isNaN`，因为`isNaN`的结果不正确。记住 NaN-check 的意图，方法是询问:“值是否等于`NaN`？不是问值是否等于“不是一个数”。

那么，在这个例子中，字符串值是否等于`NaN`？答案是否定的，结果应该是`false`。

```
const value = 'string';

isNaN(value) // true ❌
Number.isNaN(value) // false ✅ 
```

## 结论

当检查一个值是否等于`NaN`时。使用`Number.isNaN`方法。不要使用`isNaN`🤓

## 资源

*   [ECMAScript: isNaN](https://www.ecma-international.org/ecma-262/5.1/#sec-15.1.2.4)
*   [ECMAScript: Number.isNaN](https://www.ecma-international.org/ecma-262/6.0/)
*   YDKJS: NaN
*   [堆栈溢出:javascript 中的 isNaN 和 Number.isNaN 混淆](https://stackoverflow.com/questions/33164725/confusion-between-isnan-and-number-isnan-in-javascript)
*   [在 JavaScript 中测试 NaN 的问题](http://adripofjavascript.com/blog/drips/the-problem-with-testing-for-nan-in-javascript.html)
*   [堆栈溢出:是数字。IsNaN()比 isNaN()](https://stackoverflow.com/questions/25176459/is-number-isnan-more-broken-than-isnan) 更破
*   [JDKJS: Number.isNaN](https://github.com/getify/You-Dont-Know-JS/blob/f0d591b6502c080b92e18fc470432af8144db610/es6%20%26%20beyond/ch6.md#numberisnan-static-function)
*   MDN 网络文档:编号。伊斯南
*   [MDN 网络文档:NaN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/NaN)
*   [MDN 网络文档:isNaN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/isNaN)
*   [Airbnb 风格指南:南](https://github.com/airbnb/javascript#standard-library--isnan)

* * *

**感谢阅读❤**
问好！[insta gram](https://www.instagram.com/samanthaming/)|[Twitter](https://twitter.com/samantha_ming)|[脸书](https://www.facebook.com/hisamanthaming) | [媒体](https://medium.com/@samanthaming) | [博客](https://www.samanthaming.com/blog)