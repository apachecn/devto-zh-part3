# 了解快速傅立叶变换(FFT)的资源

> 原文：<https://dev.to/downey/resources-for-understanding-fast-fourier-transforms-fft-2hdj>

在我目前正在上的研究生算法课程中，我们学到了很多关于 T2 快速傅立叶变换和 T4 分治算法的知识。尽管我已经在最高层次上了解了 FFT(就像你可能用它来从信号中分离频率一样)，但这是我第一次深入了解它，我发现它相当具有挑战性。谢天谢地，如果你知道在哪里可以找到，网上有很多关于这个主题的资源。而且那是大*如果*。

这篇文章是我试图收集一些我认为特别有用的资源。它们是按照从最少的必备知识到最多的知识的顺序排列的。

## 什么是快速傅立叶变换？

我班上的讲座和教科书主要从数学的角度来处理 FFT，我发现很难理解*它如何在实践中有所帮助。下面这个来自 3 蓝 1 棕的[视频在这个高度上帮助我解释了它:](https://www.youtube.com/watch?v=spUNpyF58BY)*

[https://www.youtube.com/embed/spUNpyF58BY](https://www.youtube.com/embed/spUNpyF58BY)

## 理解团结的第 n 个根是什么

我遇到的另一个问题是理解团结的第 n 个根源。由于我较弱的比赛背景，我没有太多机会接触到[复杂位面](https://en.wikipedia.org/wiki/Complex_plane)，老实说，我不认为我听说过“统一之根”这个术语事实证明，它们对 FFT 非常重要，许多资源认为它们是必备知识。我发现[下面的视频](https://www.youtube.com/watch?v=1BjDKRrR8-E)有助于我达成共识:

[https://www.youtube.com/embed/1BjDKRrR8-E](https://www.youtube.com/embed/1BjDKRrR8-E)

## 用手做傅里叶变换

我发现有用的下一件事是如何使用傅立叶变换手动相乘两个多项式的分步指南。大多数例子用变量来解释问题的一般解决方案，而不是用具体的例子来解释。有人与我分享了以下 StackExchange 帖子，我发现这正是我所需要的。

*   [演示如何手工进行 FFT 运算](https://cs.stackexchange.com/a/103626/3082)

## 分而治之理解 FFT 讲座

最后，我看了一些其他节目的讲座，看看这些材料是如何以不同的方式解释的。我发现埃里克·德梅因的讲座很有用，但只是在消化了我之前提到的一些其他资源之后。

[https://www.youtube.com/embed/iTMn0Kt18tg](https://www.youtube.com/embed/iTMn0Kt18tg)

我还找到了来自佐治亚理工学院不同班级的[这些讲座](https://classroom.udacity.com/courses/ud061/lessons/3495828730/concepts/11787587860923)([CS 6505:可计算性，复杂性&算法](https://www.omscs.gatech.edu/cs-6505-computability-algorithms))，但我相信你需要创建一个免费的 Udacity 账户来观看它们。

## 总结

我通常更喜欢自己解释主题(参见我的[用动态编程解决背包问题](https://dev.to/downey/solving-the-knapsack-problem-with-dynamic-programming-4hce)的帖子)来巩固我自己的理解，但对于这一个，我自己仍然在努力解决这一点！同样，我发现这个资源列表很有帮助，希望通过把它们都记录在一个地方，其他人(包括我自己)将来会更容易找到。