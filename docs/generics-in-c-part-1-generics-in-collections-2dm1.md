# C#中的泛型(第 1 部分——集合中的泛型)

> 原文：<https://dev.to/seantwie03/generics-in-c-part-1-generics-in-collections-2dm1>

## 简介

泛型类型是在 2005 年引入 C# 2 的。自 2005 年以来，泛型已经成为 C#的一个关键部分，增强了列表和 Async/Await 等功能。如果您在版本 2 之后开始学习或使用 C#，您可能每天都在使用泛型，而并不真正知道它们是什么。本系列([第二部分](https://dev.to/seantwie03/generics-in-c-part-2-creating-generic-methods-2pho)，[第三部分](https://dev.to/seantwie03/generics-in-c-part-3-generic-classes-1e96))将尝试为 C#中的泛型带来一些清晰性。

泛型的一个常见用法在列表<t>中。其中<t>指定了一个**类型参数**。</t></t>

[![ListOfT](img/549a808568b3788884a334ac2065d3f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ss2cfdy7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1e9hogg1esczsbsj41lr.jpg)

## 在泛型之前- C# 1

在深入研究泛型之前，考虑一下它们能解决什么样的问题会有所帮助。最好的方法是回头看看 C# 1。在泛型之前，C#有数组和数组列表。

若要声明数组，必须指定数组的类型以及它将包含的元素数量。声明类型是非常有益的，拥有强类型是 C#的好处之一，但是预先声明大小可能会有问题。当您创建新的更大的数组并将旧值复制到数组中时，这可能会导致大量重复的代码来管理数组的大小，并且不要忘记在方法结束时将数组缩小到最终大小。

数组列表是可调整大小的，所以它们解决了这个问题，但是它们接受任何 Object 类型的东西。这意味着处理数组列表中的值需要造型或装箱。

[![ArrayListOfEveryType](img/608a9d48901ed3edb208437791d5c0c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SDO34R49--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fc1fh3vfw6ng8fh9wa6p.jpg)

## 泛型解决列表问题

系统中的列表。通用名称空间给了我们两个世界的精华。列表是强类型的，可调整大小。要声明一个列表，必须将一个参数传递给类型参数(类型参数用< >符号包围)。该参数确定列表的类型。声明后，只允许将该类型的对象添加到列表中。

[![ListOfInt](img/cb20d6257463ce06a722dffbb5f52aca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--If7R_0vW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kff4ih0dhs14xrm60kyv.jpg)

一旦声明为列表<int>，我们就不能添加()任何其他类型到列表中。这种强类型正是泛型真正强大之处。</int>

列表也可以动态调整大小。只需调用 Add()或 AddRange()方法，就可以根据需要动态调整列表的大小，以适应元素的数量。

继续[第二部分](https://dev.to/seantwie03/generics-in-c-part-2-creating-generic-methods-2pho)学习如何编写自己的泛型方法。