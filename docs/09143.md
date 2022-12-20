# 什么是数组？

> 原文：<https://dev.to/codetips/what-are-arrays-3b12>

> 数组是数据的集合。

[![What are arrays?](img/804d81c38adbedae3b29c60bc2f249e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IrnPzwQ3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/03/arrays_written-list.jpg)

在你阅读这篇文章之前，我们建议你阅读一下[什么是变量](https://codetips.co.uk/beginner/what-is-a-variable/)、[什么是循环](https://codetips.co.uk/beginner/what-are-loops/)和[什么是数据类型](https://codetips.co.uk/beginner/what-is-a-data-type/)的文章。

在[什么是循环](https://codetips.co.uk/beginner/what-are-loops/)的文章中，我们介绍了拥有人员“列表”的概念。

```
recipients = James, John, Jimmy, Jane, Jake, Jemma, Joanne, Juliet, Jessica, Josh 
```

在大多数语言中，这个“列表”是使用数组创建的。数组只是数据的集合。

数组由元素组成。元素是数组中的一个项目。在上面的例子中，有 10 个元素(`James`是第一个元素，`John`是第二个元素等等)。

[语法上](https://codetips.co.uk/beginner/what-is-syntax/)一个数组通常用方括号(`[`和`]`)表示，每个元素之间用逗号分隔。数组可以保存任何[数据类型](https://codetips.co.uk/beginner/what-is-a-data-type/)。

我们的收件人列表显示了姓名列表，这些姓名是字符串。为了将这个列表转换成一个数组，我们需要使用引号(`"`)来解释它们。

```
recipients = ["James", "John", "Jimmy", "Jane", "Jake", "Jemma", "Joanne", "Juliet", "Jessica", "Josh"] 
```

用其他数据类型创建数组也很容易。

```
integers = [1,6,3,5,98]

floats = [2.4, 56.1, 46.1]

booleans = [true, true, false, true, false]

mixed = [5, true, 2.4, false, 3, 64.2] 
```

数组元素有相应的编号(索引)。该编号用于指定特定的元素。在大多数编程语言中，索引是从零开始的。使用我们的收件人示例，如下所示:

```
recipients = ["James", "John", "Jimmy", "Jane", ... ]
index        |   0   |   1   |   2    |   3   | ... 
```

如上所述，您可以使用索引以任意顺序定位特定元素:

```
array[2] // => Jimmy

array[0] // => James

array[3] // => Jane 
```