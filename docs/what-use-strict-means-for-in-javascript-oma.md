# JavaScript 中的“使用严格”是什么意思

> 原文：<https://dev.to/chokri/what-use-strict-means-for-in-javascript-oma>

”严格模式”；

这是 ECMAScript 5 的一个特性，它可以防止某些动作被执行，并在 JavaScript 代码中抛出更多的异常。

如果您要编写复杂的代码，那么严格模式是必要的。如果你有计算算法，它会引入更好的错误检查。没有这个模式，你可以写 console.log(Infinity = 0 ),这没有问题。这将返回 0 作为检查结果，并且错误“类型错误:“无穷大”是只读的”

我们可以将这一特性转化为一个函数。例如:

> ```
> // Non-strict code... (function(){ "use strict"; // Define your library strictly... })(); // Non-strict code... 
> ```

但是，如果你激活它，你会在控制台上看到很多问题。为了避免这种情况，请尝试编写干净的代码。

帖子[在 JavaScript](https://khalifa.tn/what-use-strict-means-for-in-javascript/) 中“使用严格”是什么意思，最早出现在 [C.Khalifa](https://khalifa.tn) 上。