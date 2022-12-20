# Python 的 except quirk

> 原文：<https://dev.to/alexbecker/pythons-except-quirk-5ble>

让我给你看看我最喜欢的 Python 怪癖。您希望这段 python 代码做什么？

```
e = 2.718
try:
    1/0
except ZeroDivisionError as e:
    pass
print(e) 
```

如果你从另一种编程语言来到 Python，你可能会期望`except`子句引入一个嵌套的作用域，所以在子句中给`e`赋值不会影响外部作用域中预先存在的`e`变量。然而，在 python 中，控制结构通常不引入嵌套的作用域(理解是个例外)，所以如果你有更多的 python 经验，你可能会认为这会打印出一个`ZeroDivisionError`实例。

实际上，在标准的 CPython 实现中，它什么也不打印；相反，最后一行会引发一个`NameError`。这是个 bug 吗？实际上，这是[有意为之的](https://bugs.python.org/issue1631942)。如果查看 except 子句生成的字节码，您会看到:

```
LOAD_CONST 0 (None)
STORE_NAME 1 (e)
DELETE_NAME 1 
```

当控制流退出`except`块时，python 从作用域中删除这个名字。为什么？因为该异常保存了对当前堆栈框架的引用，该框架包含了范围内的所有内容。由于 python 主要通过引用计数来管理内存，这意味着在下一轮垃圾收集运行之前，当前范围内的任何内容都不会被释放。当前的行为是内存使用、实现的简易性和语言的整洁性之间的折衷。这有点像疣，但我认为它体现了我喜欢 Python 的一个方面:不要让纯粹妨碍实用性。

但这只解释了`DELETE_NAME`指令。如果 CPython 要在之后立即删除它，为什么要将`e`设置为`None`？好吧，假设您和 CPython 团队有相同的想法，并决定在您的`except`块:
的末尾清除异常引用

```
try:
    1/0
except ZeroDivisionError as e:
    ...
    del e 
```

在您的`except`块的末尾，CPython 将尝试删除名称`e`——您已经删除了它！为了解决这个问题，CPython 在删除`e`之前分配了`e = None`，以保证`e`的存在。