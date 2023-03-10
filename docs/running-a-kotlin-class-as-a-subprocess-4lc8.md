# 将 Kotlin 类作为子进程运行

> 原文：<https://dev.to/lankydandev/running-a-kotlin-class-as-a-subprocess-4lc8>

上周我写了一篇关于将 Java 类作为子进程运行的帖子。这篇文章是由于我需要在没有预构建 Jar 的情况下从测试中运行一个类而引发的。我在那篇帖子里写的和实际发生的唯一区别是语言。我用科特林写了那个测试。不是 Java。因此，我决定在我之前所写的基础上写这篇后续文章，重点关注启动一个 Kotlin 子流程，而不是 Java 子流程。

让我们从作为子进程 :
运行 Java 类的[中的`exec`的等价实现开始](https://dev.to/lankydandev/running-a-java-class-as-a-subprocess-34lm)

```
@Throws(IOException::class, InterruptedException::class)
fun exec(
  clazz: Class<*>, 
  args: List<String> = emptyList(), 
  jvmArgs: List<String> = emptyList()
): Int {
  val javaHome = System.getProperty("java.home")
  val javaBin = javaHome + File.separator + "bin" + File.separator + "java"
  val classpath = System.getProperty("java.class.path")
  val className = clazz.name

  val command = ArrayList<String>()
  command.add(javaBin)
  command.addAll(jvmArgs)
  command.add("-cp")
  command.add(classpath)
  command.add(className)
  command.addAll(args)

  val builder = ProcessBuilder(command)
  val process = builder.inheritIO().start()
  process.waitFor()
  return process.exitValue()
} 
```

Enter fullscreen mode Exit fullscreen mode

简单解释一下，因为我在之前的[帖子](https://dev.to/lankydandev/running-a-java-class-as-a-subprocess-34lm)中已经谈过了。

Java 可执行文件的路径被检索并存储在`javaBin`中。使用它以及传入的`clazz`、`args`、`jvmArgs`和进程的类路径，`ProcessBuilder`创建并执行一个命令。该类现在作为子进程成功运行。

上面的代码基本上是 Java 实现的复制和粘贴。不同之处在于函数参数的顺序。我决定交换一下`args`和`jvmArgs`，这样我就可以充分利用它们的默认值。这是基于你更有可能提供`args`而不是`jvmArgs`的假设。当从 Kotlin 运行时，这并不是什么大问题，因为您可以在调用函数时命名参数。但是，如果任何 Java 代码需要调用这个函数，那么参数的顺序可能会有所帮助(加上`@JvmOverloads`)。

下面是一些调用`exec` :
的方法

```
exec(MyProcess::class.java, listOf("3"), listOf("-Xmx200m"))
// jvmArgs = emptyList()
exec(MyProcess::class.java, listOf("3"))
// args = emptyList()
exec(MyProcess::class.java, jvmArgs = listOf("-Xmx200m"))
// both args and jvmArgs = emptyList()
exec(MyProcess::class.java) 
```

Enter fullscreen mode Exit fullscreen mode

用 Kotlin 而不是 Java 编写时，一个显著的区别是可以定义`main`函数的方式很多。这是因为 Kotlin 提供了许多不同的路径来创建静态函数。

我上面提供的函数将在以下两种情况下运行`main`函数:

*   在一个伴随对象中并用`@JvmStatic`标注

```
class MyProcess {
  companion object {
    @JvmStatic
    fun main(args: Array<String>) {
      // do stuff
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   在一个对象内部，用`@JvmStatic`标注

```
object MyProcess {
  @JvmStatic
  fun main(args: Array<String>) {
    // do stuff
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Kotlin 中创建静态函数的另一种方法是在类之外定义它:

```
@file:JvmName("MyProcess")

package dev.lankydan

fun main(args: Array<String>) {
  // do stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

需要提供一个名称，以便 Java 知道如何处理它。在这个代码片段中执行`main`函数是需要这样做的一种情况。不幸的是，`exec`函数在这种情况下不起作用，因为`MyProcess`类实际上并不存在。阻止它编译。有趣的是，从 Java 代码中调用`MyProcess`将无需任何更改就能编译。

为了解决在 Kotlin 版本中发现的编译错误，`exec`的参数需要稍微改变:

```
@Throws(IOException::class, InterruptedException::class)
fun exec(
  className: String, 
  args: List<String> = emptyList(), 
  jvmArgs: List<String> = emptyList()
): Int {
  // className passed into command
} 
```

Enter fullscreen mode Exit fullscreen mode

这一改变避免了最初的`exec`函数出现的编译错误。但是，这也带来了不是真正的类型安全的缺点，可能会在传入无效字符串的地方导致一些错误。如下图所示:

```
exec("dev.lankydan.MyProcess", listOf("argument"), listOf("-Xmx200m")) 
```

Enter fullscreen mode Exit fullscreen mode

这种变化真的让我很不舒服。因此，提供这两种重载可能是最明智的做法。

总结一下，尽管我要说的关于将 Kotlin 类作为子进程执行的大部分内容已经在[将 Java 类作为子进程运行](https://dev.to/lankydandev/running-a-java-class-as-a-subprocess-34lm)中介绍过了。由于 Kotlin 的灵活性，两者略有不同。Kotlin 中的`main`函数可以有多种定义方式，不幸的是，其中一种方式与其他方式都满意的`exec`函数不兼容。幸运的是，解决方案是一个小的代码变更，如果`String`和`Class`类名都有重载的话，它可以很好地工作。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！