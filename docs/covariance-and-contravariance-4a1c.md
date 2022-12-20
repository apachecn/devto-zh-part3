# 协方差和逆变

> 原文：<https://dev.to/rvprasad/covariance-and-contravariance-4a1c>

在下面的 [Kotlin](https://kotlinlang.org/) 代码片段中，*生产者类在类型参数 T* 中是协变的，*消费者类在类型参数 T* 中是逆变的。换句话说， *T 是生产者*的协变类型参数， *T 是消费者*的协变类型参数。那么，这些说法是什么意思呢？

为了理解这些陈述的含义，让我们考虑一下允许的行为。将上面的代码片段复制粘贴到[https://try.kotlinlang.org](https://try.kotlinlang.org)并运行它。您将在第 13 行和第 15 行看到两个错误。