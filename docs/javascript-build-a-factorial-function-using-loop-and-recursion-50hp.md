# Javascript:使用循环和递归构建阶乘函数。

> 原文：<https://dev.to/yongliang24/javascript-build-a-factorial-function-using-loop-and-recursion-50hp>

# 概观

n 的阶乘意味着 n*(n-1)*(n-2)*(n-3)...，所以 5 的阶乘是 5*4*3*2*1，等于 120。在本文中，我们将使用 javascript 通过简单的 for 循环和简单的递归来实现阶乘。

## For 循环解决方案

[https://repl.it/@YongLiang/OtherFoolishSoftwareagent?lite=true](https://repl.it/@YongLiang/OtherFoolishSoftwareagent?lite=true)

这是一个简单的循环，它迭代 n 次，并乘以增量 I 以得到结果。

## 递归解

递归函数应该有两个基本组成部分- 1。基础案例；2.递归情况。当满足基本情况条件时，函数将停止，否则，函数将继续调用自己，类似于循环如何迭代。

[https://repl.it/@YongLiang/PristineFamiliarPiracy?lite=true](https://repl.it/@YongLiang/PristineFamiliarPiracy?lite=true)

这里 n 是按顺序打印的，因为每次函数调用自己时，它就减 1，直到 n 达到 1。注意，每次这个函数调用自己时，n 值都会更新。

## 骑士之旅使用回溯

[![](img/b73eec97abfd5f5501990e3ee04223d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YHf3HD7P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6iet6gsydzo95ccdk8c0.gif)
[https://www . geeks forgeeks . org/the-knights-tour-problem-backtracking-1/](https://www.geeksforgeeks.org/the-knights-tour-problem-backtracking-1/)