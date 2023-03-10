# JavaScript (ES5)数组

> 原文：<https://dev.to/martyhimmel/javascript-es5-arrays-3hha>

*此文最初于 2016 年 12 月 28 日发布在我的网站 [https://martyhimmel.me](https://martyhimmel.me) 。像 dev.to 上的许多其他人一样，我决定将我的技术博客帖子转移到这个网站。*

## 快速回顾

首先，快速回顾一下数组。下面是一个示例数组:`var animals = ['cat', 'dog', 'hawk', 'wolf', 'elephant'];`

这就是我们访问这些元素的方式:

*   `animals[0]` -猫
*   `animals[1]` -狗
*   `animals[2]` -霍克
*   `animals[3]` -狼
*   `animals[4]` -大象
*   `animals[animals.length - 1]` -大象

在上面的例子中，数组的索引和访问方式有一些值得注意的地方。

1.  数组是零索引的。`animals`数组索引看起来像`[0, 1, 2, 3, 4]`。
2.  从数组中获取元素的方法是调用数组名，后跟方括号，方括号内是索引号。
3.  `arrayName[0]`将总是获取数组的第一个元素
4.  在一个数组上调用`.length`(一个数组方法)将给出一个数组中元素的总数。这是*不是*基于零，而是实际计数。`animals.length`是 5。
5.  由于数组长度和从零开始的索引不同，数组的最后一个元素总是`arrayName.length - 1`。
6.  要获得数组的最后一个元素，可以使用`arrayName[arrayName.length - 1]`。

现在基本的东西都已经介绍完了，让我们更深入地研究数组的使用、循环(迭代)和操作。

## 迭代

在你理解了循环之后，数组变得更加有用——在编码的时候，它们往往是相辅相成的。考虑这个例子:

```
var names = ['John', 'Jane', 'Judy', 'Joe', 'Jack', 'Jessie', 'Julie'];
for (var i = 0; i < names.length; i++) {
  console.log(names[i]);
}
// John
// Jane
// Judy
// Joe
// Jack
// Jessie
// Julie 
```

Enter fullscreen mode Exit fullscreen mode

如果你*真的*想的话，你可以手动输入每一个(例如 console.log(names[0])，console.log(names[1])等等。)，但是，除了繁琐的工作之外，想象一下如果数组中有 100 个元素。1000 呢？你明白了。不仅如此，手动执行还需要知道数组中元素的数量。大多数时候，在真实世界的应用程序中，您不会知道数组有多大，而且它可能会随着时间而变化。

让我们更进一步，将条件句融入其中。

```
var randomData = ['John', 1234, 'Jane', true];
for (var i = 0; i < randomData.length; i++) {
  if (typeof randomData[i] === 'string') {
    console.log(randomData[i]);
  }
}
// John
// Jane 
```

Enter fullscreen mode Exit fullscreen mode

当然，这是一个虚构的例子，但是希望您可以看到数组有多么强大，尤其是在与循环和条件一起使用时。

## 阵列方法

让我们看看几个内置的数组方法。这些方法中的每一个都将修改它们被调用的数组并返回一个值。

## `array.pop()`

移除数组的最后一个元素并返回它的值。

```
var numbers = [1, 2, 3, 5, 8, 13];
var lastNumber = numbers.pop();
console.log(lastNumber); // 13
console.log(numbers); // [1, 2, 3, 5, 8] 
```

Enter fullscreen mode Exit fullscreen mode

## `array.push(element)`

将一个元素添加到数组的末尾，并返回新的长度。

```
var numbers = [1, 2, 3, 5, 8, 13];
var newLength = numbers.push(21);
console.log(newLength); // 7
console.log(numbers); // [1, 2, 3, 5, 8, 13, 21] 
```

Enter fullscreen mode Exit fullscreen mode

## `array.shift()`

移除数组的第一个元素并返回它的值。

```
var numbers = [1, 2, 4, 8, 16];
var firstElement = numbers.shift();
console.log(firstElement); // 1
console.log(numbers); // [2, 4, 8, 16] 
```

Enter fullscreen mode Exit fullscreen mode

## `array.unshift(element)`

将一个元素添加到数组的开头并返回新的长度。

```
var numbers = [1, 2, 4, 8, 16];
var firstElement = numbers.unshift(0.5);
console.log(firstElement); // 6
console.log(numbers); // [0.5, 1, 2, 4, 8, 16] 
```

Enter fullscreen mode Exit fullscreen mode

## 关闭思绪

这四个数组方法是一些更常用的内置数组方法。再来两个，`map`和`reduce`也是函数常用的。我将在以后的函数教程中介绍这些内容。同时，你可以在 [Mozilla 的数组开发文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)中找到更多的数组方法。