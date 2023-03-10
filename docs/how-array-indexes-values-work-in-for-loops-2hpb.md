# 数组索引和值如何在 for 循环中工作

> 原文：<https://dev.to/manumagalhaes/how-array-indexes-values-work-in-for-loops-2hpb>

*第一部分【编码第一个月的诡异问题】*

[![](img/3dca08013b50f32b9bb000d2136808a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3b8BbcUl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/960/1%2A2pN23CkyqyJ5GTzNP51cjg.jpeg)

当我开始学习 JavaScript 时，我曾经对数组迭代感到困惑。我不太清楚我是在处理数组索引、值还是两者都是。你有同感吗？所以跟着我。我采取了一种疯狂的方法，因为嘿，如果你已经阅读了文档，它不工作，你可能需要一些不寻常的东西。

我假设你知道什么是数组 [*，如何调用数组值*](https://www.w3schools.com/js/js_arrays.asp) *，这是循环* *的* [*的语法，以及什么是*](https://www.w3schools.com/js/js_loop_for.asp) [*JavaScript 运算符*](https://www.w3schools.com/js/js_operators.asp) *，即使你还不知道如何正确使用它们。*

让我们研究一下这整个动力学是如何工作的。如果你很着急，跳到 TL；DR；最底部的部分。

## 为循环

让我们用两个不同的数组，一个是数字，另一个是字符串，并检查索引/值/循环是如何交互的。对于这个例子，让我们假设你买了五根漂亮的小黄瓜，每根都标有一个质数。因为你疯了，你想知道所有数字的总和。

```
1 let primes = [2,3,5,7,11]
2   
3 let total = 0;
4 for (let cucumber=0; cucumber<primes.length; cucumber++) {
5 total += **primes[cucumber]**;
6 }

_// 28_ 
```

是的，你猜对了:每根黄瓜现在是一个索引，每个质数现在是一个数组值。

你看到第 5 行发生了什么吗？primes[cucumber]正在调用标记在 cucumber 上的**值**。在一个更加文档化的例子中，primes[cumber]将与 arr[i]相同。

不过第四行是什么黄瓜？注意，for 循环引用 cucumber (index)来提取标记在它上面的数字(值)。所以第 4 行的意思是:

*   设 cucumber = 0 *计算机，当 for 循环第一次执行**total+= primes【cucumber】时，我要你因子的第一根黄瓜就是位置 0 的黄瓜。*计算机服从并翻译素数【黄瓜】为素数【0】。哒哒！
*   但是注意了，计算机，我希望你在我们用完黄瓜之前立即停止计数。电脑点头。
*   顺便说一下，每次你提取完黄瓜中的数字并加到总数中，对下一根黄瓜重复这个操作。一个都不要跳过，我一个都要！电脑微笑。

在 unbonkered speak 中，当这个代码块运行时，这是*种*发生的情况:

*   计算机看到字母 total = 0。

1.  计算机进入 for 循环。它看到 index 为 0，所以它转到 primes[0]，提取它的值并将其添加到 let total。第一次循环后，total 更新为 2，因为 2 + 0 = 2。
2.  计算机将索引增加 1，并检查新的索引是否小于数组长度。什么事？所以它重复上面的步骤 1。当它到达索引等于数组长度的点时，它离开 for 循环，Chrome 返回 28。

## 爽。但是，字符串呢？

对于字符串，这是相同的动态，有一个明显的扭曲:因为字母和单词不是数字，它们并不真正“相加”，它们只是简单地并列。这就是为什么像*、/或-这样的运算符在处理字符串时会返回 NaN。

[![](img/d00610f4d7da2bee4214a430bc24c80d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6g9ffw2D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/466/1%2AhvDKheO2VLprbTfeGs89_g.png)

## TL；DR；

当您使用**进行循环**时，您实际上是在处理**索引**，以此来提取和操作数组中的**值**。

* * *