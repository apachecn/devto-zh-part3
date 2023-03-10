# 安全访问 Kotlin 中的 lateinit 属性

> 原文：<https://dev.to/rahulchowdhury/safely-accessing-lateinit-properties-in-kotlin-ool>

按照设计，Kotlin 不允许非空变量在声明期间未初始化。

如果你一直在挖掘科特林，你会知道一个`lateinit`属性可以让你克服这个障碍。

然而，如果你是一个完全的新手，`lateinit`允许你先声明一个变量，然后在应用程序执行周期的某个时候初始化。

这些看起来都很美好，那么问题出在哪里呢？

当您试图访问未初始化的属性时，会出现此问题。让我们多谈谈那个。

## 访问未初始化的属性

每当你声明一个`lateinit var`，你需要在访问它之前初始化它。否则，您将会遇到一个奇特的异常，如下所示:

```
Exception in thread "main" kotlin.UninitializedPropertyAccessException: lateinit property fullName has not been initialized
    at UninitializedPropertyKt.main(UninitializedProperty.kt:3) 
```

不要把这误认为是微不足道的例外。它会让你的应用崩溃。

这是很常见的情况。以此为例*:*

 *您可能有一个从远程服务器获取的列表数据，并且需要基于该数据初始化一个`RecyclerView`适配器。

当您在从远程服务器实际获取数据之前尝试访问该适配器时，会发生什么情况？

嘣！你的应用崩溃了。

那么，如何解决这个问题呢？

## 采取菜鸟方式

这个问题最有利的解决方案是使属性成为常规的可空属性，而不是 lateinit var，并在以后赋值。

你可以这样做:

```
var fullName: String? = null 
```

然后只要在访问值的时候做一个简单的空检查。

```
if (fullName != null) {
    print("Hi, $fullName")
} 
```

有点像 Java。但是等一下，Kotlin 应该比 Java 更好。此外，Kotlin 的 USPs 之一是消除由`NullPointerException`引起的惨败。

那么，为什么要走传统路线呢？

这里有一个更好的解决方案。

## 走科特林尼式的道路

如果您使用的是 Kotlin 1.2，您可以很容易地检查一个`lateinit`变量是否已经初始化。如果不是，那么，你总是可以使用非空方法。

不管怎样，下面是你如何检查一个`lateinit var`是否已经被初始化:

```
if (::fullName.isInitialized) {
    print("Hi, $fullName")
} 
```

根据宣布本次更新的[官方博文](https://blog.jetbrains.com/kotlin/2017/09/kotlin-1-2-beta-is-out/)显示，这种方法使用反射来检查值是否已经初始化。

另外，一个`deinitialize`方法将在未来的版本中推出，可能是在 Kotlin 1.3 中。

## 这里的动机是什么？

当我第一次遇到本文提到的异常时，我有两种选择:

*   走传统的非空路
*   做一些很酷的事

我采用了后一种方法。

我不想在我的代码中加入空检查。还有，这个看起来更有意义。

传统就是，嗯，*传统*。随波逐流。

*本文最初发表于 [upcurve.engineering](https://upcurve.engineering) 。**