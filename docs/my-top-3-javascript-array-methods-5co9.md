# 我最喜欢的 3 种 JavaScript 数组方法

> 原文：<https://dev.to/georgehanson/my-top-3-javascript-array-methods-5co9>

JavaScript 是一种超级通用的语言。我喜欢它，这几年它真的走了很长的路。有了 JavaScript，我们现在可以构建强大的单页应用程序，在构建这些应用程序时，您会在某个时候使用数组。

我喜欢数组，这里是我经常使用的三个 JavaScript 方法。

## 1。阵列过滤器

这实际上是在 ES5 中添加的一个方法，但它仍然是我经常使用的一个方法。阵列过滤器允许我们基本上做它在锡上所说的。根据我们提供的规则将数组过滤为子集。

为此，您只需在数组上调用 filter 方法，并向它传递一个回调。例如，在这个数组中，我过滤结果，只返回年龄小于 18 岁的对象。

```
const people = [
  { name: 'Jon', age: 23 },
  { name: 'Daenerys', age: 23 },
  { name: '﻿Arya', age: 17 }
]

const children = people.filter(person => person.age < 18)
// Returns [ { name: 'Arya', age: 17} ] 
```

## 2。数组查找

这是添加到 ES6 规范中的内容。它类似于 array filter 方法，但它返回的是经过筛选的数组的第一个结果。

对于这个方法，我们再次向它传递一个回调，包含我们的过滤器的规则。然后它会过滤结果，并返回子集中的第一个项目。在这种情况下，我想在人的数组中找到第一个成年人。

```
const people = [
﻿  { name: 'Jon', age: 23 },
  { name: 'Daenerys', age: 23 },
  { name: '﻿Arya', age: 17 }
]

const adult = people.find(person => person.age >= 18)
// Returns { name: 'Jon', age: 23 } 
```

你会注意到这次它返回的是对象，而不是包含对象的数组。

## 3。数组 ForEach

forEach 方法是在 ES5 中引入的，因此有足够的浏览器支持。然而，我仍然看到人们不使用这种方法，而是使用 for 循环的代码。

forEach 方法完全按照您的想法来做，它循环遍历数组中的每一项，以便您对其运行回调。这类似于 map 方法，但是有一个关键的区别。当您使用数组映射时，它会根据您传递给它的回调返回一个新数组。而对于 forEach 方法，它不返回任何内容。

map 和 forEach 方法都有它们的用例。但是如果我不打算操作数组，我通常会使用 forEach 方法。

在下面的例子中，我只是在数组上调用 forEach 方法，并将对象传递给一个自定义函数。

```
const people = [
﻿  { name: 'Jon', age: 23 },
  { name: 'Daenerys', age: 23 },
  { name: '﻿Arya', age: 17 }
]

people.forEach(person => sendNotification(person)) 
```

## 结论

这就是我最喜欢的三个数组方法。不要误解我的意思，还有很多其他真正有用的数组方法，我恳请你去查阅一下。但这是我最喜欢的三个，也是我最常用的三个。

你有经常使用的不同的数组方法吗？一定要让我知道。