# Python 列表理解一览

> 原文：<https://dev.to/lachlaneagling/python-list-comprehensions-at-a-glance-59ki>

[![](img/e9d65e9fbd4301dfdcc5bfeda7ab8470.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u6f-inzC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjuZZe0SbI8E_QPd74Ax9uA.jpeg)

在过去的 12 个月里，我才开始真正深入地研究 Python 并经常使用它，我不断地发现大量可用的语言特性，这些特性使 Python 使用起来如此愉快。这些小宝贝中的一个是列表理解，这篇文章是关于列表理解的快速入门，它们是什么，如何使用它们，好处，当然还有什么时候不使用它们。

### 什么是列表理解？

Python 中的 List comprehensions 是从另一个集合构建列表的另一种方法，它们最常用于需要对现有集合的每个元素进行某种形式的操作的情况。这提供了一个比传统的 Python for 循环更简洁明了的解决方案。

这个特性早在 2000 年 7 月就作为 [PEP202](https://www.python.org/dev/peps/pep-0202/) 的一部分被添加到 Python 版本 2 中。

[官方 Python 列表理解文档](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions)。

### 那么……我怎么写一个呢？

列表理解由表达式组成，后跟循环语法和可选的 if 或 if/else 条件，所有这些都包含在一组方括号中。将列表理解赋给一个变量将给予该变量由列表理解产生的列表值。

需要注意的一点是，在列表理解中使用 if 和 if_else 条件时有一个微妙的区别，当只使用 if 条件时，它放在循环的末尾。然而，当它是 if_else 时，它位于表达式和循环之间。