# 闭包/作用域和循环问题的设置超时

> 原文：<https://dev.to/levimeahan/closures-scope-and-the-settimeout-for-loop-question-5bl6>

我以前研究过 JavaScript 中的事件循环，对它的工作原理有一个大致的了解，但是最近我被这个经典的面试问题弄得措手不及。

```
// What does the below code output?

for(var i = 0; i < 4; i++) {
   setTimeout(function() {
      console.log(i);
   }, i * 1000);
}

// Answer: 4 4 4 4 
```

我见过这种情况，记得这是一个棘手的问题，有一些方法可以解决它，如使用`let i = 0;`而不是`var i = 0;`，但我并不从根本上理解为什么会这样。(如果你想对这个问题有一个很好的、非常详细的解释，可以看看这个帖子:[https://medium . freecodecamp . org/thrown-for-a-loop-understanding-for-loops-and-times-in-JavaScript-558d 8255 D8 a4](https://medium.freecodecamp.org/thrown-for-a-loop-understanding-for-loops-and-timeouts-in-javascript-558d8255d8a4))

但是还有一件事我不明白。回调被放入事件队列中，所以为了使用它，它当然必须保存一份`i`的副本，对吗？既然我们的主代码将在回调被调用之前结束运行，那么变量`i`不应该不存在了吗？我没有看到解释，所以我去看了 MDN 关于[关闭](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)的文章。

这篇文章解释了——闭包(也就是我们传递给 setTimeout 的回调)保持对创建它们的环境/作用域的引用，包括对其变量的引用，即使在该环境/作用域停止运行之后。哦。突然间，这变得更有意义了。即使在我们的主代码完成之后，对它的变量(至少是闭包使用的变量)的引用仍然存在，以供闭包访问。因此，如果回调中使用的`i`是该环境中的一个全局变量，闭包将使用该引用。

因此，这个问题的许多解决方案都围绕着为每次调用 setTimeout 时存在于**中的`i`创建一个不同的作用域，这样事件队列中的每个回调都维护一个对完全不同的变量的引用，我们实际上在不同的作用域中有 4 个名为`i`的不同变量。**

这实际上引出了以下解决方案的一个有趣但不完全明显的行为:

```
for(let i = 0; i < 4; i++) {
   setTimeout(function() {
      console.log(i);
   }, i * 1000);
}

// 0 1 2 3 
```

为了实现这一点，我们知道每次调用 setTimeout 时,`i`实际上需要是一个不同的变量。这意味着当我们在这样的 for 循环中使用`let`时，它实际上是在循环的每次迭代中创建一个名为`i`的新变量。从来没有这样想过！

这也提出了一些需要记住的重要的性能知识——如果我们维护一个对闭包的引用，我们就维护了对定义它的整个作用域的引用(即使有可能的编译器优化，它至少是闭包中使用的变量)。

更多解释闭包和作用域的阅读材料:

[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Statements/let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)

[https://github . com/getify/You-Dont-Know-JS/tree/master/scope % 20% 26% 20 closures](https://github.com/getify/You-Dont-Know-JS/tree/master/scope%20%26%20closures)