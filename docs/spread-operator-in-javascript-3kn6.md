# 扩展运算符(...)在 JavaScript 中。

> 原文：<https://dev.to/uddeshjain/spread-operator-in-javascript-3kn6>

你好，只是一个快速提醒，这是系列的第三部分**奇怪的 JS** 所以如果你还没有检查出来，去读前两个帖子。

所以不要再浪费时间了，让我们开始吧。今天我们要谈论的是**扩散运算符**，但我称它们为**超级圆点**，因为我喜欢它。🙃

现在最大的问题是黑客攻击的是什么**传播算子**？？

用最简单的话来说, **Spread 操作符**将一个列表转换成一个数组，将一个数组转换成一个列表。听起来很困惑？

我们用一些例子来了解一下。假设你创建了一个有 2 或 3 个参数的函数，你刚刚调用了这个函数，但是你有一个数组，而函数需要一个链表。比如说。

```
function sum(a, b, c) {
    console.log(a + b + c)
}

let array = [1, 2, 3]
sum(...array)

// 6 
```

Enter fullscreen mode Exit fullscreen mode

这是一个场景，但它可以用于多种情况，如数组操作。

您可以连接两个数组。

```
const num = [1,2,3,4]
const words = ['Hey', 'Hellow']

console.log([...num, ...words])

//[1, 2, 3, 4, "Hey", "Hellow"] 
```

Enter fullscreen mode Exit fullscreen mode

这只是两个例子，但它可以是许多用例，所以用**扩展操作符**来弄脏你的手。

在那之前，我会带着新奇的东西回来**再见**。
T3![Bye](img/01be876d4bc1df237208698b5201f483.png)T5】