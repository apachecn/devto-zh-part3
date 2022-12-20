# 带有:pre 特殊形式的 Cleaner Clojure 函数

> 原文：<https://dev.to/kevgathuku/cleaner-clojure-functions-with-the-pre-special-form-5aca>

在编程过程中，经常会遇到这样的情况，您希望确保函数的参数满足某些条件，如果不满足，就抛出一个错误。在这篇文章中，我们将看到 Clojure 如何提供一个方便的特殊表单来大大简化这个任务。

我最近遇到的一个这样的场景是试图提出一个计算 3x + 1 问题的函数(Collatz 猜想)。问题如下:

> 取任意正整数 n。
> 
> 如果 n 是偶数，将 n 除以 2 得到 n / 2。
> 
> 如果 n 是奇数，n 乘以 3 再加 1 得到 3n + 1。
> 
> 无限重复这个过程。

该猜想指出，无论从哪个数字开始，最终总会达到 1。

手头的任务是编写一个函数，该函数将一个数字作为参数，并返回该数字最终为 1 所需的步骤数。

您还会注意到，要传递的数字的第一个条件是它必须是正整数。因此，给定任何小于 1 的值，该函数都会抛出一个错误。

Clojure 中的典型解决方案如下所示: