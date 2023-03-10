# 如何在 JavaScript 中找到对象数组中的最大 id

> 原文：<https://dev.to/therealdanvega/how-to-find-the-max-id-in-an-array-of-objects-in-javascript-4k50>

我最近为一些学生编写了一个教程，我遇到了一个问题，我相信我们都曾经遇到过。我有一个对象数组，每个对象都有一个 id。我需要一种快速有效的方法来查看所有 id 并找到最大 id。

在本文中，我将带您了解 4 种不同的解决方案。

*   Array.forEach
*   Array.map
*   Array.reduce
*   Math.max

## 设置您的项目

我使用 Node 来运行这个例子，但是你可以很容易地使用 JavaScript 并把它放到一个网页中。我要做的第一件事是要求 Node 的 [assert 模块](https://nodejs.org/api/assert.html)，这使我们能够提供一组简单的断言测试。

```
const assert = require("assert"); 
```

接下来，您将创建一个字符数组。我最近读了《恶灵》这本书，所以我决定用书中的一些角色。旁注:如果你还没看过那本书，我强烈推荐。数组中的每个字符都将是一个包含 id、名字和姓氏的对象。

```
const characters = [
  { id: 1, first: "Tim", last: "Draper" },
  { id: 17, first: "David", last: "Boies" },
  { id: 199, first: "Tim", last: "Kemp" },
  { id: 75, first: "Henry", last: "Mosley" },
  { id: 444, first: "Elizabeth", last: "Holmes" },
  { id: 95, first: "Donald", last: "Lucas" },
  { id: 186, first: "Larry", last: "Ellison" },
  { id: 364, first: "Channing", last: "Robertson" },
  { id: 285, first: "Charles", last: "Fleischmann" },
  { id: 33, first: "John", last: "Howard" }
]; 
```

在每个可能的解决方案中，您都希望编写一些逻辑来返回数组中最大的 id。

## Array.forEach

您的第一个想法可能是使用某种类型的循环来迭代数组，为什么不呢，这是您开始编写代码时就被教导要做的事情。您可以从声明一个零的最大值开始，遍历每个字符，如果它的 id 大于最大值，就更新最大值。

```
let max = 0;
characters.forEach(character => {
  if (character.id > max) {
    max = character.id;
  }
});
assert(max === 444); 
```

我每次写代码的主要目的都是让某些东西先工作起来，然后再改进它。如果你要运行这段代码，它当然可以工作，但对我来说似乎不太对劲。如果数组中有一千或一百万个对象呢？每当我开始使用某种类型的循环迭代一个数组来执行 som 计算时，这对我来说是一个巨大的危险信号。

## Array.map

每当迭代数组的危险信号出现时，我马上问自己这是 map/filter/reduce 能解决的问题吗？因此，如果你打算在这里采用这种方法，你可以从[映射方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)开始。`map()`方法创建一个新数组，其结果是在调用数组中的每个元素上调用一个提供的函数。您将使用 map 方法创建一个只包含 id 的新数组，这样您就不再使用对象了。此时，你可以在数组上使用普通的排序方法，获取列表中的最后一个元素，这就是你的 max id。

```
const ids = characters.map(user => user.id);
const sorted = ids.sort((a, b) => a - b);
assert(sorted[sorted.length - 1] === 444); 
```

如果你想变得更好，你可以在一条语句中完成所有的事情。

```
assert(
  characters.map(user => user.id).sort((a, b) => a - b)[
    characters.length - 1
  ] === 444
); 
```

## 阵列缩小

`reduce()`方法对数组的每个成员执行一个 reducer 函数(您提供的),产生一个输出值。在这里，您将对字符数组调用 reduce。您将定义一个累加器来累加回调的返回值。每次调用回调函数时，它都会返回一个值并存储在 max 中。回调方法查看字符 id，如果它大于 max，则返回，否则返回 max。最后，它需要为 max 设置一个默认值，这就是`characters[0].id`正在做的事情。

```
const maxId = characters.reduce(
  (max, character) => (character.id > max ? character.id : max),
  characters[0].id
);
assert(maxId === 444); 
```

如果你注意到这是一个类似于我们使用 forEach 方法的方法，迭代每个元素并与 max 进行比较。这里的区别是使用 reduce 要快得多。如果你愿意，你也可以使用 reduce 和下一个解决方案`Math.max()`的组合。

## 传符

虽然我更喜欢 map 和 reduce 方法，而不是迭代数组，但我仍然感觉不太好。如果你深入研究 [Math.max](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max) ，你会发现它可以接受一个数字列表或者一个数字数组。

这意味着您所要做的就是获得一个 id 数组，然后您可以将它传递给`max()`函数。如果你注意的话，你之前已经用地图做到了。这意味着您可以使用 map 函数获得一个 id 数组，然后使用 spread 操作符将其传递给`max()`函数。

```
assert(Math.max(...characters.map(user => user.id)) === 444); 
```

> Spread 语法允许在需要零个或多个参数(对于函数调用)或元素(对于数组文本)的地方扩展可迭代对象，例如数组表达式或字符串，或者在需要零个或多个键值对(对于对象文本)的地方扩展对象表达式。

对我来说，这是看起来最干净的解决方案，我碰巧真的很喜欢它。

## 性能结果

我并不想把这篇文章变成一篇关于 JavaScript 函数式编程的文章，但这就是我们的落脚点。我做了一些每个函数的基本计时(使用`console.time()` & `console.timeEnd()`)，发现了以下结果。

*   迭代测试:0.217 毫秒
*   地图测试:0.191 毫秒
*   缩减测试:0.144 毫秒
*   扩散测试:0.148 毫秒

## 结论

虽然使用 reduce 给了我们非常小的性能优势，但我更喜欢在这里使用 Math.max 和 spread 操作符。对我来说，它看起来更干净，这是我喜欢写的东西。如果给你这个问题，你会寻求什么解决方案？我错过什么了吗？我希望你喜欢我将如何解决这个问题，直到下一次...

快乐编码
丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*