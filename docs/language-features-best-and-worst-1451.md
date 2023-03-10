# 语言特点:最好和最差

> 原文：<https://dev.to/awwsmm/language-features-best-and-worst-1451>

我对构建自己的编程语言很感兴趣，我想知道:**任何编程语言中你最喜欢和最讨厌的特性是什么？**

以下是我的一些想法:

* * *

当我创建自己的语言时(希望很快)，我希望它能模仿 **R** 的范例，其中**标量只是长度为 1 的向量** :

```
>  x  <-  3  >  length(x)  [1]  1  >  x  <-  c(1,2,3)  >  length(x)  [1]  3 
```

Enter fullscreen mode Exit fullscreen mode

...这意味着(正如你在上面看到的)你可以在标量上使用类似`length()`的方法，标量实际上只是长度为 1 的向量。我想把它扩展到任何维度和长度的矩阵，这样每一位数据实际上都是一个 N 维矩阵。这将统一任何维度数据的数据处理。(当然，性能会受到影响。)

* * *

我也喜欢 **Haskell** 的`Integer`类型，这是一个易于使用的**无限精度整数** :

```
ghci> let factorial n = product [1..n]

ghci> factorial 100
93326215443944152681699238856266700490715968264381621468592963895217599993229915608941463976156518286253697920827223758251185210916864000000000000000000000000 
```

Enter fullscreen mode Exit fullscreen mode

Java 有 [`BigInteger`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/math/BigInteger.html) 和 [`BigDecimal`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/math/BigDecimal.html) 分别是任意精度的整数和浮点数。由于用户从不*输入*一个无限精度的浮点数，应该可以跟踪输入和使用的数字，并且当用户打印或导出数据到文件时，只对结果进行舍入/截断。您还可以跟踪有效数字[并将其用作打印时的默认精度。](https://en.wikipedia.org/wiki/Significant_figures)

例如，想象一下，不是计算`x = 1/9`并在某个时候截断结果以存储在变量`x`中，而是保存一个用于构造`x`的公式的记录。如果您随后声明一个变量`y = x * 3`，您可以将公式作为`y = (1/9) * 3`存储在内存中，或者识别用户输入的整数`3`和`9`，并将`y`的公式表示内部简化为`1/3`。

(在我看来，如果这是用编程语言实现的，那就意味着没有“变量”这种东西，真的。每个变量都是一个小函数，每次使用时都会被调用和计算。)

抛开这种简化，您可以让`y`在计算时引用`x` *，并实现一个类似于[电子表格的功能](https://stackoverflow.com/questions/36949834/c-tie-one-variable-to-two-others-via-references)，其中更新一个变量会对其他变量产生连锁反应。当`print`变量时，您可以显示计算出的值，但是当`inspect`变量时，您可以显示用于计算的公式。或者什么的。*

* * *

最后，一个我永远也不想模仿的语言特性是 [**Java 的数字层级**](https://docs.oracle.com/javase/tutorial/java/data/numberclasses.html) 。

在 Java 中，有像`int`、`float`、`boolean`这样的*原语*，它们**不是**Java 类层次结构的一部分。这些是为了模仿 C 的基本数据类型，可以用于快速计算。它们是仅有的几种不是从 Java 的总体`Object`类派生出来的类型。由于 [Java 不支持运算符重载](https://stackoverflow.com/questions/1686699/operator-overloading-in-java/1686714)，算术运算`+`、`-`、`*`、`/`等只为原语定义(而`+`为`String` s 内部重载)。所以如果你想做数学，你需要这些基本类型之一...明白了吗？

好吧，Java 也有*包装类*用于每一个原始类型:`Integer`、`Float`、`Boolean`等等。(另请注意，`int`由`Integer`而不是`Int`包裹。为什么？我不知道。如果你有，请在评论中告诉我。)这些包装类允许您对数字执行正确的 OOP。Java[“boxes”和“unboxes”](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)数字会自动输入，因此您不必手动将`Integer`转换为`int`来进行运算。

越来越糟了！Java 中的数字类层次结构是扁平的，这意味着所有的数字类(`Byte`、`Integer`、`Double`、`Short`、`Float`、`Long`)都直接从[、`Number`、](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Number.html)继承而来，而`Number`、*除了将给定的`Number`转换为特定的原始类型之外，不实现任何方法*。这意味着，例如，如果你想做一些简单的事情，如*定义一个方法来找出两个`Number`的最大值*，你需要首先将每个数字转换成一个`Double`，然后使用`Double.max()`进行比较。您需要转换成`Double`，这样您就不会在转换成“更小”的类型时丢失精度(假设您没有同时接受`BigInteger` s 或`BigDecimal` s，这会使这变得更加复杂)。`Number`(看在上帝的份上)甚至没有实现 Java 的`Comparable`接口，这意味着如果没有 Java 将它们拆箱成原语，[将`float`隐式地转换成`double`](https://www.javainterviewpoint.com/type-casting-java-implicit-explicit-casting/) 然后执行比较，你甚至不能*比较* a `Float`和 a `Double`。

我不希望 Java 的等级制度出现在我最大的敌人身上。