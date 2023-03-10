# C++，Python 等的函数式编程。

> 原文：<https://dev.to/nahiyan/functional-programming-in-c-python-etc-4d33>

假设我想打印到屏幕上。在像 Haskell 或 Elm 这样的纯函数式编程语言中，您可以简单地将它留给运行时引擎来完成。你的函数不需要做脏活。

如果我继续使用 python、C++等非函数式编程语言，我就不能向运行时环境发出信号，在屏幕上打印文本、生成随机数、与数据库对话等。我必须自己做手术。这使得函数不纯，因为它们改变状态和/或依赖外部状态来工作。

既然 python、C++、JS 等语言缺乏动作、命令等特性来让函数与运行时环境通信，我如何用这些语言编写纯函数呢？

我更喜欢用简单的例子来学习。如果可能的话，你能告诉我如何使用非函数式语言(最好是 python 或 C++)编写一个在屏幕上打印文本的纯函数吗？