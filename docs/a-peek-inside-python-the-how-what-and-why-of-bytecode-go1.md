# Python 内部的一瞥——字节码的方式、内容和原因

> 原文：<https://dev.to/lachlaneagling/a-peek-inside-python-the-how-what-and-why-of-bytecode-go1>

在这篇文章中，我们将深入介绍什么是 Python 字节码，如何查看它，以及如何阅读和理解它。

Jeppe Hove Jensen 在 Unsplash 上拍摄的封面照片]

## Python 字节码是什么？

和很多使用过 Python 的人一样，你可能只习惯看到 Python 源代码(。py 文件)，但是我们的 Python 源代码发生了什么变化才能被 CPU 执行呢？

与许多其他解释编程语言的方式类似，Python 首先将其源代码编译成中间字节码格式，然后由 Python 运行时解释，并随后转换成本机 CPU 指令。中间字节码指令存储在 pycache(.pyc)文件，Python 运行时在执行程序时会使用这些文件。

## 如何查看生成的字节码。

Python 标准库提供了 *dis* 模块，该模块公开了用于将 Python 源代码分解成字节码指令的 API。

官方文档:[dis—Python 字节码的反汇编器](https://docs.python.org/3/library/dis.html)。

我们可以在这个模块中利用 *dis(obj)* 函数打印出作为参数传入的对象的反汇编字节码。

下面是一个简单的 *hello_world()* 函数的例子，它已经被使用 *dis()* 函数反汇编。