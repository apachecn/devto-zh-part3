# 哈斯克尔是什么？

> 原文：<https://dev.to/lautarolobo/what-s-haskell-1om3>

所以，你是 [Haskell](http://wikipedia.org/wiki/Haskell_programming_language) 的新手，你想了解基础，太好了！

Haskell 是一种*纯函数式* [编程语言。](https://en.wikipedia.org/wiki/Programming_language)这是什么？变量不会随着时间而改变，变量会一直固定在给定的值上。所以，如果你用相同的参数调用一个函数两次，输出将总是相同的。这个属性被称为*参照透明度*。它让你评估一个函数，并且知道它是否正确。

Haskell 被认为是一种懒惰的编程语言。这意味着它将只做显示一段代码的结果所需的最少的计算(如果你告诉他显示它)。

同样重要的是，Haskell 是*静态类型的*并且有*类型的推断*，所以，如果你不指定函数的[类型](https://en.wikipedia.org/wiki/Data_type)，Haskell 会巧妙地弄清楚。而且，如果你弄乱了类型，[编译器](https://en.wikipedia.org/wiki/Compiler)会告诉你错误，大多数情况下，会具体告诉你在哪一行所有的东西都爆炸了。这有助于在开发的早期阶段修复错误，因此一旦发布，程序失败的概率将会降低。