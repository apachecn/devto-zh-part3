# 一个简单的 Haskell 程序

> 原文：<https://dev.to/gefjon/a-simple-haskell-program-1h0a>

今天，我想分享一个简短的 Haskell 程序，它帮助我提高了对这种语言的理解，希望你们都能对它有所了解。

```
module Main where

data EndlessList a = Cons a (EndlessList a)

repeatForever :: a -> EndlessList a
repeatForever x = Cons x (repeatForever x)

genSequence :: a -> (a -> a) -> EndlessList a
genSequence init trans = Cons init (genSequence next trans)
    where next = trans init

naturalNumbers :: EndlessList Integer
naturalNumbers = genSequence 0 succ

printAllOf :: Show a => EndlessList a -> IO b
printAllOf (Cons a b) = putStrLn (show a) >> printAllOf b

main :: IO ()
main = printAllOf naturalNumbers 
```

我知道 Haskell 很疯狂，所以我会尽我所能，尽可能详细地解释。你可以看到它是如何在 Godbolt 上编译[的。](https://godbolt.org/z/eGCUaj)

如果你已经安装了 [`ghc`](https://www.haskell.org/ghc/) ，你应该能够将那个源文件复制到一个名为`Main.hs`的文件中，用`ghc Main.hs`编译它，用`./Main`运行它。如果你这样做了，你会看到它打印自然数，从 0 开始，直到你用`<ctrl>-c`结束这个过程才停止。

```
module Main where 
```

Haskell 的`module`和 Javascript 的非常相似。重要的是，任何想要构建成我们可以运行的可执行文件的程序都必须定义一个名为`Main`的模块，该模块包含一个值`main :: IO ()`。我会在源文件的底部找到它。

```
data EndlessList a = Cons a (EndlessList a) 
```

这条线做几件事。从右向左阅读:

*   `data`报表定义新类型
*   `EndlessList`将是我们新的“类型构造函数”的名字
*   `a`将是`EndlessList`的一个参数——把`EndlessList`想象成一个从一个参数返回一个类型的函数
*   `=`将类型构造函数(`EndlessList a`)与“值构造函数”`Cons a (EndlessList a)`分开
*   `Cons`是“值构造函数”的名称。给你的类型构造函数和值构造函数取相同的名字是很常见的，比如`data EndlessList a = EndlessList a (EndlessList a)`，但是我觉得使用不同的名字增加了这个例子的清晰度。“cons”这个名字来自 Lisp，在函数式编程中用来表示一对
*   `a`和`(EndlessList a)`表示各自类型的值构造器`Cons`的两个参数。总而言之，我们只剩下两样东西:一个类型`EndlessList a`和一个名为`a -> EndlessList a -> EndlessList a`的`Cons`的函数(Haskell 就是这样写函数类型的。例如，将两个数相加的函数可能具有类型`Int -> Int -> Int`。最后一种类型，后面没有箭头，是返回类型；其他都是论据)。

您可能马上会注意到一些奇怪的事情——没有明显的方法来构造、存储或操作一个根据定义必须是无限长的值。尽管如此，`ghc`还是能够运行这段代码，并且它确实立即开始打印数字，而不是停止。

```
repeatForever :: a -> EndlessList a
repeatForever x = Cons x (repeatForever x) 
```

该模块定义了一个功能`repeatForever`。第一行描述了它的类型:对于任何类型`a`，接受类型`a`的值并返回一个`EndlessList a`。第二行告诉如何计算它:首先用`x`调用构造函数`Cons`，然后用递归调用`repeatForever x`的结果。这个函数是无限递归的，如果你试图用 Javascript/C/C++/Java 编写它，它要么崩溃，要么永远不会终止。然而，在 Haskell 中，懒惰求值是王道，`repeatForever x`完全按照您的要求去做:永远重复`x`。

```
genSequence :: a -> (a -> a) -> EndlessList a
genSequence init trans = Cons init (genSequence next trans)
    where next = trans init 
```

这个功能很像`repeatForever`，但是更酷。如果你能读懂那种字体，对你有好处。`init`和`next`都是`a`的类型，`trans`是一个接受`a`并返回`a`的函数。这个函数创建一个列表，它的第一个元素是`init`，第`n`个元素是将`trans`应用到第`(n - 1)`个元素的结果。

让我们来测试一下:

```
naturalNumbers :: EndlessList Integer
naturalNumbers = genSequence 0 succ 
```

这里，`naturalNumbers`是`Integer` s 的一个`EndlessList`，众所周知，自然数都是从 0 开始的，后继函数`succ`会计算下一个自然数。我们可以用匿名函数`\n -> n + 1`代替`succ`，但我认为在我的代码中写`succ`很有趣。

Haskell 的难以琢磨的关注点分离将输入/输出归入一个叫做`IO`的`Monad`中。比我更聪明的人尝试过描述单子是什么以及它们的含义，但都失败了，所以我就不尝试了。

相反，我将只告诉你类型`IO b`意味着一个输入/输出动作，它将产生类型`b`的值。

```
printAllOf :: Show a => EndlessList a -> IO b
printAllOf (Cons x y) = putStrLn (show x) >> printAllOf y 
```

`printAllOf`将对任何可能是`show` n 的类型`a`采取一个`EndlessList a`并执行一些`IO`动作，导致任何任意类型`b`。现在，因为我们的`EndlessList`将是无限的，这个 I/O 操作将永远不会终止，这就是为什么它可以声明产生任何类型的值`b`。

这里我们有一个递归`IO`动作，而不是递归函数。像`foo >> bar`一样，`>>`中缀操作符表示一系列操作。我把`foo >> bar`读作“做`foo`并丢弃结果，然后做`bar`”。`putStrLn`是一个`IO`动作，它将一个`String`输出到标准输出，后跟一个换行符，类似于 Javascript 的`console.log`。`show`将一个值格式化为`String`，所以`putStrLn $ show x`将`x`转换为`String`然后打印到标准输出。

```
main :: IO ()
main = printAllOf naturalNumbers 
```

`main`，像 C/c++/Rust/Java/等中的。，是一个程序的“入口点”。在 Haskell 中，`main`是一个没有结果的 IO 动作，这意味着它的类型是`IO ()`。我们将我们的定义为打印所有的自然数，这就是我们的程序所做的。

#### 编辑

本帖原码用了

```
printAllOf (Cons x y) = (putStrLn $ show x) >> printAllOf y 
```

按照阿里伦的建议，我把那句话换成了:

```
printAllOf (Cons x y) = putStrLn (show x) >> printAllOf y 
```