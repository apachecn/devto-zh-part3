# args 和 kwargs 是什么鬼东西？

> 原文：<https://dev.to/wrrnwng/what-the-heck-are-args-and-kwargs-3jbm>

所以你正在看某人的 Python 代码，看到一些疯狂的东西，比如`*args`和`**kwargs`。那些是什么鬼东西？

我们必须打开它，因为有几样东西可能会引起混乱。首先，`*`和`**`是做什么的？那么，`args`和`kwargs`是什么意思呢？最后，它是用来做什么的？

`*`是解包`list`或`tuple`数据结构的一种方式，而`**`是解包`dict`的一种方式。`args`如你所料，意味着争论。现在`kwargs`可能更难猜测，但是你可以推断出“kw”是“关键字”的意思。这样，您可以猜到`args`表示传递给函数的参数，用逗号分隔，`kwargs`是关键字参数。

如果您试图在 Python 中创建一个高阶函数，那么使用`args`和`kwargs`的主要原因非常简单。使用`args`和`kwargs`而不是传入一堆命名参数和关键字参数，可以一般化函数签名，这样你就可以适应几乎任何函数。

#### 引用

[解包参数列表](https://docs.python.org/3.7/tutorial/controlflow.html#unpacking-argument-lists)