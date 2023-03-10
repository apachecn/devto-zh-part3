# 大 O 符号:初学者的简要概述

> 原文：<https://dev.to/madisonstankevich/big-o-notation-a-brief-overview-for-the-beginner-1o13>

在我向编程过渡的过程中，大 O 符号是一个令我难以置信地害怕的概念。我有一个先入为主的想法，认为大 O 符号涉及复杂的数学，需要知道大量的算法。事实证明，大 O 符号并不可怕，事实上，一旦你做了一点点研究，它就很有意义。

这篇博文旨在为有基本编码经验但没有接触过大 O 符号的人提供一个高层次的概述。它以抽象的方式解释了大 O 的概念基础。

## 有什么大不了的？

大 O 符号是一种描述性工具，开发人员使用它来表达他们的程序处理输入增加的能力。它可以衡量一个程序运行的速度(“时间复杂度”)或者它占用了多少存储空间(“空间复杂度”)。

本质上，如果一个程序能够在与少量输入相同的时间内处理越来越多的输入，那么根据大 O 符号，它将被评为优秀。

[![Graph of Big O Notation](img/4335c1dace1ab7289339b2fbf7c949f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ark_FZG1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1omv0tmikzeaj24z8ps2.jpeg)

## 复杂性的度量

假设我们有一个返回字符串“Hello World”的函数。这个函数以数组的形式接受用户输入。不管输入的数组是空的还是有几千个数字长，程序仍然有相同的运行时间。这被称为“恒定时间”，被认为是理想的。

对数组中的每一项执行任务的函数呢？也许现在我们的同一个函数被修改了，不再不管输入是什么都返回一次“Hello World ”,而是对数组中输入的每一项都返回一次“Hello World”。这种类型的函数具有所谓的“线性增长”。这意味着输入的大小和我们程序的运行时间有直接的关系。

现在让我们说，不是为函数中的每一项都返回“Hello World ”,我们的程序已经被重写，为数组中的每个元素返回“Hello World”的次数与数组的长度一样多。在此示例中，大小为 1 的数组将返回“Hello World”一次，大小为 2 的数组将返回“Hello World”四次，大小为 3 的数组将返回“Hello World”九次，依此类推。这被认为是“指数增长”。你可以想象增加输入会极大地阻碍我们程序的速度。

[!["That escalated quickly"](img/42391c515b2ec2b0bc6dfdb257a11e4c.png)](https://i.giphy.com/media/ToMjGpjpXMFPshSYGLm/giphy.gif)

## 为什么重要？

大 O 符号很重要，因为我们希望我们的代码是“可伸缩的”。可伸缩性是程序在面对大量输入或数据时保持高效的能力。

因此...这是否意味着每个程序都有一个算法是完美的大 O 符号解决方案，可以使我们的代码高效且可伸缩？

[!["Cinderella's slipper](img/04c1efc21f9ee1eeb402760aa14212bb.png)](https://i.giphy.com/media/qL1Ig46CJ8XAc/giphy.gif)

总之...没有。

写代码没有一个放之四海而皆准的算法。您编写的代码类型将取决于您正在解决的问题。不同的问题会有不同的要求，因此使用不同的方法。

作为一名开发人员，认识到程序的复杂性并删除不必要的代码以使代码尽可能高效地运行是很重要的。大 O 符号是可以用来表达代码复杂性的工具之一。

## 资源

*   http://www.corejavainterviewquestions.com/idiots-guide-big-o/
*   https://rob-bell.net/2009/06/a-beginners-guide-to-big-o-notation/