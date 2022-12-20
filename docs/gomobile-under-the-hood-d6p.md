# 引擎盖下

> 原文：<https://dev.to/_tink3r/gomobile-under-the-hood-d6p>

这篇文章最初发表在我的博客

<center>
![](img/d6aae4c5084cf2e793af2572ba0ea595.png)
</center>

<center>
Photo by Nathan Van Egmond on Unsplash
</center>

为了理解和充分欣赏 Gomobile 的功能，我们需要浏览一些与 Java/Objective-C 相关的概念(特性),并看看 Gomobile 是如何将它们结合在一起的。主要是 Java 和 Objective-C 与 c / c ++通信的能力，以及 go 生成 C 共享库的能力。

#### C 和 C++支持 Java 和 Objective-C

Java 和 Objective-C 应用程序能够与用 C 和 c ++编写的库进行交互已经有一段时间了。

Java -

使用 JNI 框架与 C 库通信。许多标准库类依靠 JNI 为开发者和用户提供功能，例如文件 I/O 和声音功能。在标准库中包含对性能和平台敏感的 API 实现允许所有 Java 应用程序以安全和独立于平台的方式访问该功能。

Objective-C-

C 的超集，并且拥有 C 库的访问权限。

#### Go 和 CGo

cgo 是一个允许 go 程序与 C 库互操作的库。我们对 cgo 提供的一个特性感兴趣，这个特性允许我们将 go 文件编译成 C 共享库。Go 1.5 引入了执行模式，允许使用 C 风格的 api 公开 Go 方法。要了解更多关于执行模式及其功能的信息-[https://docs . Google . com/document/d/1nr-TQHw _ er 6 goqrsf 6t 43 gghfdelrap 0 nqss _ 00 rgzq/edit？pli = 1 # heading = h . 44 N2 lm 20 ate 5](https://docs.google.com/document/d/1nr-TQHw_er6GOQRsF6T43GGhFDelrAP0NqSS_00RgZQ/edit?pli=1#heading=h.44n2lm20ate5)

Cgo 对将 go 文件编译成共享库有一些要求。

1.  该包必须是主包。编译器将把包及其所有依赖项构建成一个共享的二进制对象。

2.  源必须导入伪包“C”。

3.  使用//export 注释来注释您希望其他语言可以访问的函数。

4.  必须声明空的 main 函数。

如果满足以下要求，则任何。go 文件可以转换成包含一个. h 和一个. so 文件的共享库。的。h 文件包含导出的方法。一个深入的例子，创造自己的。所以还有。h 文件可在此处获得-[https://medium . com/learning-the-go-programming-language/calling-go-functions-from-other-languages-4c 7d 8 bcc 69 BF](https://medium.com/learning-the-go-programming-language/calling-go-functions-from-other-languages-4c7d8bcc69bf)

#### 那么 Gomobile 是做什么的？

我们知道-

1.  Java 和 Objective-C 可以和 C 对话
2.  使用 cgo 我们可以创建一个共享库，它可以被其他 C 文件或者 Java 和 Objective-C 通过(1)调用

将这两个概念结合起来，生成一个应用程序可以用来调用 Go 代码的库。

示例代码(目标-C 示例)

1.  如果我们有一个文件 hello.go ->
    `package hello
    func HelloWorld() {}`

2.  当我们运行 gomobile bind - target=ios 时，会创建一个包含 Hello.framework 的 hello . framework。目标——c . h 和二进制叫你好。你好。Objective-C.h 是一个头文件。

3.  现在我们知道已经生成了一个共享库，并且我们知道我们的 hello.go 不满足由 cgo 转换 go a 文件的要求。这就是 Gomobile 的用武之地，它生成一个符合 cgo 要求的代理 go 文件，然后运行 cgo 工具来创建一个公共头文件和一个共享库，然后可以被 Java 和 Objective-C 调用。aar 档案馆。

#### 参考文献-

1.  [https://medium . com/learning-the-go-programming-language/calling-go-functions-from-other-languages-4c 7d 8 bcc 69 BF](https://medium.com/learning-the-go-programming-language/calling-go-functions-from-other-languages-4c7d8bcc69bf)
2.  [https://docs . Google . com/document/d/1nr-TQHw _ er 6 goqrsf 6t 43 gghfdelrap 0 nqss _ 00 rgzq/edit？pli=1#](https://docs.google.com/document/d/1nr-TQHw_er6GOQRsF6T43GGhFDelrAP0NqSS_00RgZQ/edit?pli=1#)
3.  [https://docs . Google . com/document/d/1y 9 hs tonl 9 wpj-5VM-xwrstuejfuaxgoxohxvas 7 gzhe/edit](https://docs.google.com/document/d/1y9hStonl9wpj-5VM-xWrSTuEJFUAxGOXOhxvAs7GZHE/edit)