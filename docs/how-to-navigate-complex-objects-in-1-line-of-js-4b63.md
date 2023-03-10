# 如何在一行 js 中导航复杂对象

> 原文：<https://dev.to/lucagez/how-to-navigate-complex-objects-in-1-line-of-js-4b63>

今天我想分享一个非常酷的一行程序，它让你在复杂的对象中导航变得轻而易举..一分钟后🤯让我们开门见山吧。

```
 const navigator = (obj, path) => path.reduce((a, b) => a && a[b], obj); 
```

给定一个对象和一个表示从父属性到目标属性的所有属性(降序)的数组，我们可以导航到我们的目标值。

原生的`Array.prototype.reduce`方法正在帮助我们实现这一点。

*你可以在这里熟悉`reduce`:*
[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array/Reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

我们将复杂对象作为初始值提供给我们的`reduce`方法。我们还提供了一个处理初始值减少的函数。

然后我们可以像这样使用`navigator`函数:

```
// Test object
const test = {
  a: {
    b: {
      c: 'test',
    },
  },
};

navigator(test, ['a', 'b', 'c']); // 'test' 
```

## 基本流程

让我们来分析一下这里发生了什么:

累加器➡在第一次执行`a`时将具有值`obj`。
➡如果`a[b]`存在，在下一次迭代中，`a`将等于`a[b]`。
➡如果`a[b]`未定义，那么`a`就是我们的目标值。

**注意:**使用逻辑 AND 运算符(& &)，如果两个值都为真，则返回第二个值。

## 寻找更方便的语法

描述嵌套路径的数组符号并不方便。幸运的是，我们可以很容易地将它升级为更方便的点符号语法。

```
// Splitting dot notation path => resulting in the array we need.
const navigator = (obj, path) => path.split('.').reduce((a, b) => a && a[b], obj); 
```

那么我们最终可以这样使用它:

```
 const test = {
  a: {
    b: {
      c: 'test',
    },
  },
};

// 🎉🎉🎉🎉
navigator(test, 'a.b.c'); // 'test' 
```

我们现在可以只用一行 js 来导航嵌套非常深的属性🎉🎉