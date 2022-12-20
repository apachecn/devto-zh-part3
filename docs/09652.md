# JavaScript 注释 I

> 原文：<https://dev.to/celisdelafuente/javascript-notes-i-ikh>

我们来匆匆回顾一下 JavaScript 的一些特性吧！

## 可重复项

可以在`for..of`中使用的对象称为 iterable。

Iterables 必须实现名为`Symbol.iterator`的方法。`obj[Symbol.iterator]`的结果叫做迭代器。迭代器必须有一个名为`next()`的方法，该方法返回一个对象`{done: Boolean, value:
any}`，该值是下一个值。`Symbol.iterator`方法由`for..of`自动调用，但我们也可以直接调用。

创建一个 iterable 或类数组 obj 的实数组，然后我们可以对它使用数组方法。可选参数 mapFn 和 thisArg 允许我们对每个项目应用一个函数。

## 解构

将对象或数组映射到变量。

对象语法:

```
let {prop : varName = default, ...} = object 
```

Enter fullscreen mode Exit fullscreen mode

数组语法:

```
let [item1 = default, item2, ...rest] = array 
```

Enter fullscreen mode Exit fullscreen mode

第一项去`item1`；第二个进入`item2`，剩下的全部让数组休息。

## 绘制和设置

`Map`，是键控值的集合，它允许对象成为键，并提供了一个`size`属性。

`Set`，是唯一值的集合，不允许对元素重新排序。

以下允许垃圾收集:还有提供垃圾收集的`WeakMap`和`WeakSet`，我需要研究更多关于它们的内容。也许下一个帖子。