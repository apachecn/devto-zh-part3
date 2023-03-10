# C#中的泛型(第 2 部分——创建泛型方法)

> 原文：<https://dev.to/seantwie03/generics-in-c-part-2-creating-generic-methods-2pho>

## 简介

本系列的第一部分是泛型的概述，探讨了为什么在列表中使用泛型。本文将解释如何创建自己的泛型方法。

## 串联任意类型的数组

我将通过为 System.Array 创建一个[扩展方法](https://www.tutorialsteacher.com/csharp/csharp-extension-method)来演示泛型方法。int[]，string[]，等等。

首先让我们看看如何从 Main()调用该方法。

[![Main](img/8710c2b12b17fa4ed2a86d6a539099a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V4Vt8K0m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0y1jou6bfssev2poz68u.jpg)

因为这是系统上的扩展方法。数组，我创建了两个数组，然后在第一个数组上调用 Concatenate 将第二个数组作为参数传递。然后，我使用一个字符串数组重复相同的步骤，以证明该方法可以在多种类型上工作。在第二部分中，我没有明确指定类型参数<string>，但是编译器可以计算出来，因为结果被赋值给一个字符串[]。</string>

现在让我们看看 Concatenate 扩展方法。

[![ArrayExtension](img/e17563507d4af2fb4049fe28e32fcd82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sy1YR3WE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ru3sr7ockfbxyvekkl46.jpg)

首先，我想指出方法名后面的**类型参数**。< T >表示这是一个泛型方法。您会注意到返回类型和方法参数也是 T。所有这些 T 都被传递给类型参数的任何参数所替换。

在 Main()的第一个代码块中，我显式地指定了类型参数，传入了<int>，因此编译器知道用 int 替换所有 T 的出现。在 Main()的第二部分，我将 Concatenate 的结果赋给了 string[]，这样编译器就知道用 string 替换所有出现的 T。</int>

继续第三部分，我将介绍如何创建泛型类。