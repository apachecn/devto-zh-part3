# “when”块中的私有范围变量

> 原文：<https://dev.to/lankydandev/privately-scoped-variable-in-when-block-idk>

关于 Kotlin 1.3 中引入的一个变化的超级短文(是的，我知道它已经发布了一段时间了)。我们将快速看一下如何将`when`块的主题捕获到一个限定了作用域的变量中。这是生活质量的提高，节省了一行左右的代码，同时使变量的角色更加清晰。

下面是您在更改前要写的内容:

```
val enum = myClass.enum
when (enum) {
  MyEnum.ONE -> println(enum.propertyOne)
  MyEnum.TWO -> println(enum.propertyTwo)
  else -> println(enum)
} 
```

Enter fullscreen mode Exit fullscreen mode

你现在可以这样写:

```
when (val enum: MyEnum = myClass.enum) {
  MyEnum.ONE -> println(enum.propertyOne)
  MyEnum.TWO -> println(enum.propertyTwo)
  else -> println(enum)
} 
```

Enter fullscreen mode Exit fullscreen mode

`enum`变量的实例化与`when`块的声明合并。`enum`现在的作用域是`when`块，不能在它之外访问。不是很大的变化，但它确实使代码看起来更整洁了。

我们再来看一个例子:

```
val obj = someObject()
when(obj) {
  is String -> println("This is a string and it says $obj")
  is Number -> println("This is a number and its value is $obj")
  else -> println("I don't know what $obj is")
} 
```

Enter fullscreen mode Exit fullscreen mode

变成:

```
when(val obj = someObject()) {
  is String -> println("This is a string and it says $obj")
  is Number -> println("This is a number and its value is $obj")
  else -> println("I don't know what $obj is")
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个问题上没有什么可说的，因为首先没有什么可谈的。你可能已经明白了，如果我自己没有解释清楚的话，Kotlin 1.3 发行说明中的例子可能对你有意义。

如果你觉得这篇文章很有帮助，你可以在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) 来了解我的新文章。