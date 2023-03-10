# JavaScript 类型化数组:意外溢出

> 原文：<https://dev.to/antogarand/javascript-typed-arrays-unexpected-overflow-1e1p>

在今天的帖子中，我将解释一种特殊的情况，如果你使用类型化数组，这种情况会在 JavaScript 中发生。

这不是我的发现，我是在 Chris Wellons 的博客文章中发现的，但是我发现这个怪癖非常有趣，我想在我自己的文章中讨论一下。

众所周知，在 JavaScript 中，所有的数字都是双精度的，除了两种情况:

*   位运算
*   类型化数组

在按位运算中，数字的小数部分被忽略。

`console.log(2.99999999999999 ^ 2);`

在本例中，答案是`0`，因为执行的操作实际上是`2 ^ 2`。

类型化数组有点类似，它们只存储不同类型的整数。

下面是一个使用 Uint8 数组的例子，它只能包含 8 位整数。

`const first = Uint8Array.of(123); const second = Uint8Array.of(456); console.log(first[0], second[0]);`

这两个日志的结果是`123`，和`200`。

`200`可能是意料之外的，但是如前所述，数组只能包含 8 位无符号整数。

8 位能存储的最大值是`255`。由于`456`大于`255`，我们导致[整数溢出](https://en.wikipedia.org/wiki/Integer_overflow)，并从 0 开始。

我们可以用下面的例子来证实这一点:

`const arr = Uint8Array.of(255); ++arr[0]; console.log(arr[0]);`

这个操作的结果是`0`，因为我们将`255`增加到`256`，因此触发了溢出。

由于我们溢出了一个数字，我们在`0`重新开始。

* * *

现在，让我们来看看我在引言中提到的有趣的怪癖。

我们已经知道，uint8 数组中的`255 + 1`是 0。

考虑到这一点，您认为下面的代码会产生什么结果？

```
const arr = Uint8Array.of(255);
const x = ++arr[0];
console.log(x, arr[0]); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码与前面代码片段的唯一区别是，我们将`++` increment 运算符的结果赋给了一个变量。

由于`arr[0]`的值是`0`，我们期望它们都是`0`，对吗？

让我们来了解一下！

`const arr = Uint8Array.of(255); const x = ++arr[0]; console.log(x, arr[0]);`

原来，`x`的值是`256`，而不是`0`！

这种奇怪的现象背后的原因是因为操作过程中的类型。

在 Javascript 中，在常规的算术运算中，我们使用`Number`类型(很快，BigInt！).由于增量运算符等同于`1 + [value]`，在运算过程中两个数字都转换为`Number`。

一旦操作完成，就会发生两件事:

1.  我们将操作的结果存储在`arr`数组中。
2.  我们将操作的结果存储在`x`值中。

注意在第 2 步中，我们是如何使用运算结果而不是`arr`中的值的！

由于结果是两个`Number`相加，我们没有造成整数溢出，因此我们的值是`256`而不是`0`！

希望你和我一样觉得这个怪癖很有趣！

如果你想了解更多，我建议你查看一下 [Chris 的博客文章](https://nullprogram.com/blog/2019/01/23/)，他在文章中将这种行为与`C`进行了比较，并提供了定义这种行为的 Ecma 规范的链接！