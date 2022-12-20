# 用 Rust 扩展 Python

> 原文：<https://dev.to/p_chhetri/extending-python-with-rust-4pna>

[![](img/1247ed8f087992bcfeaf46a1a262f333.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---EbjGIA9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Am9mLGeyjmV7Flq-t5MA62g.png)

### 简介:

Python 是一种很棒的编程语言，但有时在执行某些任务时，它会有点慢。这就是为什么开发人员一直在[构建 C/C++扩展](https://docs.python.org/3/extending/building.html)并将它们与 Python 集成以提高性能。然而，编写这些扩展有点困难，因为这些低级语言不是类型安全的，所以不能保证定义的行为。这往往会引入内存管理方面的错误。Rust 确保内存安全，因此可以轻松防止这类错误。

### 慢蟒场景:

Python 速度慢的许多情况之一是构建大型字符串。在 Python 中，string 对象是不可变的。每次将一个字符串赋给一个变量时，都会在内存中创建一个新对象来表示新值。这与 Perl 等语言形成了鲜明的对比，在 Perl 等语言中，可以就地修改字符串变量。这就是为什么用几个小段构造一个长字符串的常见操作在 Python 中效率不是很高的原因。每次追加到字符串末尾时，Python 解释器必须分配一个新的字符串对象，并将现有字符串和追加字符串的内容复制到该对象中。随着被操纵的管柱变大，这个过程会变得越来越慢。

问题:编写一个函数，该函数接受一个正整数作为参数，并返回一个字符串，该字符串将从零到该整数的一系列整数串联起来。

因此，让我们尝试用 python 解决上述问题，看看我们是否可以通过 Rust 扩展它来提高性能。

### Python 实现:

#### 方法一:天真追加