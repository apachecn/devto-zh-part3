# Java 中更好的空检查

> 原文：<https://dev.to/scottshipp/better-null-checking-in-java-ngk>

*埃斯蒂·扬森斯在 Unsplash 上拍摄的照片*

Java 运行着世界上一些最大的网站和平台，但是我经常为它作为一种语言的设计而挣扎。

然而，它是一种很好的完成工作的语言。我已经将 Java 投入生产，在像 Expedia 和 T2 open market 这样的公司工作时，它每秒可以处理数千个请求。Java 编译器，以及围绕该语言的强大生态系统中的优秀开发人员工具，防止了所有类型的应用程序错误。

太糟糕了，Java 应用程序中最常见的失败是 NullPointerException。每当 JVM 试图解引用一个变量并发现 null 而不是一个对象时，就会抛出一个 *NullPointerException* 。你如何防止这种情况发生？答案很简单:不要有任何指向 null 的变量。

不幸的是，Java 是一种令人讨厌的语言，它实际上迫使程序员创建(或通过方法参数接收)引用 null 的变量。任何已声明但未初始化的变量都会自动引用 null，而其他 Java 语言构造(如 try/catch)会迫使变量必须在外部作用域中声明，其中 null 是唯一有效的选择之一。

这里有一个常见的例子，来自一个被认为是好 Java 的官方 Java 教程: