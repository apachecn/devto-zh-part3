# 编程陷阱

> 原文：<https://dev.to/lefebvre/programming-pitfalls-18e8>

代码不在乎你是编程新手还是老专家，是公民开发者还是工程主管，一些失误可能会抓住我们任何人。请继续阅读，了解一些最常见的编程陷阱以及如何避免它们。

## 忽略编码

当处理来自(或发送自)应用程序外部的字符串时，您总是需要考虑文本的编码。如今，UTF8 是最常见的文本编码，可能也是您应该经常使用的编码。但是，如果您从数据库、web 服务或其他不受您控制的文件中获取文本，那么您可能希望使用 DefineEncoding 方法来设置传入文本的正确编码，或者使用 ConvertEncoding 来将编码转换为您更愿意使用的编码，比如 UTF8。

## 避免异常处理

有些类会在意外发生时引发异常。如果你的代码忽略了这些异常，这将导致你的应用程序向用户显示一个错误信息，迫使他们退出应用程序。那不是最好的体验。

使用 Try…Catch 代码来确保异常被捕获，并得到适当而优雅的处理。

举个例子，如果调用 XMLDocument。LoadXML 并提供无效的 XML，则会引发 XMLException。通过捕捉这个异常，您可以向用户显示一条消息，告诉他们选择的 XML 文件无效，并要求他们选择另一个文件。

## 跳过数据库错误检查

无论何时运行数据库命令，都有可能发生数据库错误。一些语言可能会对此提出异常，但其他语言可能会要求您检查错误。

这确实有助于捕捉微妙的问题，比如 SELECT 语句中的拼写错误。

## 忽略内存泄漏

应用程序中的内存泄漏是指它一直保留内存，但从未归还。这通常并不明显，因为增加的内存使用量很小，通常不会影响 64 位应用程序。此外，当你的应用程序退出时，内存被释放。但是，如果您有一个重大的内存泄漏，您应该考虑如何消除它。

您可以通过检查(使用操作系统任务管理器或活动监视器)应用程序的内存使用量是否随着应用程序的使用而显著增加来确定应用程序是否存在内存泄漏，即使您正在关闭不再使用的窗口或文档。

有可能你有一些对象永远不会变为零，因此不会释放它们的内存。通常情况下，您不必担心手动将对象设置为 Nil，因为内存管理器(垃圾收集和自动引用计数很常见)会在不再使用对象时清理它们。但是有一种情况，称为循环引用，会导致对象不被设置为空。

循环引用意味着 ObjectA 引用 ObjectB，ObjectB 引用 ObjectA。因为它们的引用计数都没有达到零，所以它们不能从内存中释放。

您可以手动将内容设置为 Nil，以确保内存被释放，也可以利用 WeakRef 类来帮助管理它。

## HiDPI 中的模糊图形

如果您的语言支持，请通过包含更大尺寸的图像来利用 HiDPI (retina)显示器。如果你只包括一个单一的图像，它可能会使用 1 倍的大小，将被缩放，看起来模糊或在 HiDPI 屏幕上模糊。相反，请确保您的项目中有多种图像大小，以便更好的图像可以在 HiDPI 屏幕上使用。

## 未更新到 64 位

对于 macOS，你肯定应该更新你的项目，以便它们构建 64 位应用。很可能今年晚些时候发布的 macOS 版本将不再支持 32 位应用程序，你需要做好准备。对于某些工具来说，这是微不足道的(在 Xojo 中，您只需更改下拉菜单选项)。

在 Windows 上没有那么匆忙，因为 64 位版本的 Windows 仍然完全支持 32 位应用程序。然而，在越来越普遍的 64 位版本的 Windows 上，您将获得更好的整体性能，因为操作系统不必加载会耗尽内存和 CPU 的 32 位兼容层。

在 Linux 上，64 位发行版变得越来越普遍，许多发行版不包含对 32 位应用程序的任何内置支持。因此，如果可能的话，你会希望能够分发 64 位应用程序。

## 剪切粘贴编码

不要盲目使用你在 Google、Stack Overflow 或 dev.to 搜索中找到的代码片段。花时间去理解代码的作用。把它当作一次学习经历。因为如果代码在将来崩溃了(不管是什么原因),而你又不知道它是如何工作的，你怎么能期望及时修复它呢？

## 不检查单据

每个人都喜欢抱怨文档，但是我发现很多时候人们在抱怨之前甚至懒得去检查它们！也许文档没有你想要的那部分代码，但是你不能指望文档为你写所有的代码。使用文档来理解某个东西是做什么的，编写小程序来测试命令和你的假设，这样你就可以编写你需要的代码。

我还建议人们试着提前阅读这些文档，以了解你能得到什么，而不是随意搜索你可能需要的东西。

我想我会就此打住，因为我可能会继续一段时间。你见过哪些陷阱？