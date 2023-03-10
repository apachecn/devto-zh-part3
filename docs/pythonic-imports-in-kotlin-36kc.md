# 科特林的蟒蛇进口

> 原文：<https://dev.to/xuhaibahmad/pythonic-imports-in-kotlin-36kc>

如果您使用 Kotlin 已经有一段时间了，您可能会遇到与 Kotlin 的保留关键字名称冲突的问题。一个常见的例子是流行的模仿库 [Mockito](https://site.mockito.org/) ，其中的`when`方法现在被 Kotlin 用于其类似开关的构造。所以你最终会做这样的事情:

```
`when`(messenger.getMessage()).thenReturn("Hello World!") 
```

另一种情况是，您希望将导入的类或扩展函数命名为更有意义和可读性的名称。这在某种程度上可以通过使用`typealias`来实现。然而，Kotlin 标准库中有一个更好的解决方案。

## python 处理进口的方式

我在`Python`中发现的最灵活的事情之一是可以给导入的类起任何你想要的名字，就像一个变量一样。所以你只需要导入一个名字适合你风格的类，比如

```
import matplotlib.pyplot as plt 
```

现在，您可以在整个脚本中使用`plt`作为变量。

# 科特林的进口-作为别名

在我最近的一个项目中遇到一些模糊问题后，我希望在 Android 开发中使用这个。经过一番搜索，我惊讶地发现科特林一直都有这个功能。就像 Python 一样，您可以简单地用一个`as`关键字添加导入。

现在让我们看看我们的 Mockito 问题是如何解决的:

```
import org.mockito.Mockito.`when` as whenever 
```

现在你可以在整个课堂上用`whenever`来代替不太令人愉快的`'when'`。

```
whenever(messenger.getMessage()).thenReturn("Hello World!") 
```

原来如此。喜欢 Kotlin(以及 Python)的另一个原因！；)

* * *

对于建议和疑问，只需[联系我](http://linkedin.com/in/xuhaibahmad)。