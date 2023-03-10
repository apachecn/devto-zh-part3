# 递归

> 原文：<https://dev.to/denisepen/recursion-1kab>

**什么是递归**

递归是一个调用自身的过程。关于编程，递归是一个调用自身的函数。

它是如何工作的？
递归函数调用自身，直到到达基格。

基本情况是递归结束的地方。没有基本情况，你的函数就不知道什么时候停止调用自己。一旦达到基本情况，函数就停止调用自己，并返回它的最终值。

**我们来看一个例子**
我们来看下面这个递归的例子:

[![Imgur](img/9bf7be966e787140b3670b59a517573a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--asVhFGEk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/DVoorfP.png)

我们有一个函数，它从作为参数提供给它的数字开始倒数，直到 1。

一旦数字达到 1
( < =0)，则*基本情况*指示函数返回。

该函数的其余部分打印出数字的当前值，然后在再次调用**倒计时**函数之前将其递减。

这个循环一直重复，直到数字达到基本情况，此时它打印出*“您已经到达终点”*，然后返回并退出该函数。

让我们用一个数字来执行这个函数:

```
countdown(4);

=> 4
=> 3
=> 2
=> 1
=> You've reached the end 
```

上面的函数也可以用一个简单的 for 循环来解决:

```
function countdown(num) {
    for (let i = num; i > 0; i--) {
        console.log(i)
    }
    console.log("You've reached the end")
} 
```

正如您在这个简单的例子中看到的，for 循环更容易使用和理解。然而，熟悉递归是很重要的，因为递归在编程中被广泛使用。此外，递归经常出现在技术性工作面试中，所以现在就花时间去学习它。