# 写出更好的代码:第二天——每个整数的乘积

> 原文：<https://dev.to/arjunrajkumar/write-better-code-dec-5--question3-4ifc>

这篇文章最初出现在 Arjun Rajkumar 的博客上。Arjun 在印度班加罗尔经营着一家网络开发公司。

-

### 第二天:问题 1

你有一个整数数组，对于每个索引，你想找到除了该索引处的整数之外的所有整数的乘积。

编写 get _ products _ of _ all _ ints _ except _ at _ index()方法，该方法采用整数数组并返回乘积数组。

例如，假设:

[1, 7, 3, 4]

您的方法将返回:

[84, 12, 28, 21]

通过计算:

[7 * 3 * 4, 1 * 3 * 4, 1 * 7 * 4, 1 * 7 * 3]

这里有一个问题:你不能在你的解决方案中使用除法！

[来自面试官的问题蛋糕](https://www.interviewcake.com/question/ruby/product-of-other-numbers?section=greedy&course=fc1)

如果你想继续下去，请随意在下面贴出你的答案。