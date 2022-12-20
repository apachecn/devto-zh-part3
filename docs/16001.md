# Java 中没有的有用的流实用程序

> 原文：<https://dev.to/scottshipp/useful-stream-utilities-not-found-in-java-13cf>

*Karan Chawla 在 Unsplash 上拍摄的照片*

## 一点脉络

标准 Java 库做出了合理的尝试，为 Java 的功能性[流](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/Stream.html)对象提供了有用的实用方法。例如，如果一个流包含空对象，一个快速过滤器可以通过引用方便的库提供的 [Objects.nonNull](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Objects.html#nonNull(java.lang.Object)) 方法来删除它们。此外，您可能希望获得一个逗号分隔的对象字符串表示列表。标准库提供了[收集器。加入](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/Collectors.html#joining())收集器来促进这一点。

您可以将这些标准库方法放在一起，这样您就可以在流中获得某种程度的优雅，否则您将无法获得这种优雅。它们允许程序员处理常见的用例，例如下面的例子，它显示了当地杂货店农产品区的水果清单:

**老祈然道**

```
StringBuilder produceDescImperative = new StringBuilder();
for(Fruit fruit : fruits) {
  if(fruit != null) {
    produceDescImperative.append(fruit).append(", ");
  }
}
produceDescImperative.deleteCharAt(produceDescImperative.lastIndexOf(", "));
return produceDescImperative.toString(); 
```

Enter fullscreen mode Exit fullscreen mode

**新功能方式**

```
return fruits.stream()
        .filter(Objects::nonNull)
        .map(Object::toString)
        .collect(Collectors.joining(", ")); 
```

Enter fullscreen mode Exit fullscreen mode

像`Objects::nonNull`和`Collectors.joining()`这样的方法为 Java 中的*可读*、*可维护*的函数式风格指明了道路，这种风格允许语言保持其个性，同时将其扩展到包括函数式习惯用法。

## 有些缺点

不幸的是，标准库似乎经常止步于开发人员优先的方向。例如，真的有必要总是手动将对象流映射到一个字符串吗，如第 3 行:

```
return fruits.stream()
        .filter(Objects::nonNull)
        .map(Object::toString) // why ?
        .collect(Collectors.joining(", ")); 
```

Enter fullscreen mode Exit fullscreen mode

为什么`Collectors.joining(", ")`返回的收集器不能自己算出`toString`调用？几乎在 Java 标准库中的任何其他地方，当期望一个明显的字符串结果时，`toString()`调用是不可见的。(例如，`System.out.println(someObject)`是一个具有预期结果的有效调用，这样程序员就不用键入“`System.out.println(someObject.toString())`”

很可能库实现者没有时间或上下文来日常使用这些实用程序并体验它们的缺点。不幸的是，我们其他人都有。这并不是说我责怪他们没有时间或背景，只是似乎有更好的方法的空间。

## 引进磨机

我更愿意提供解决方案，而不是问题，我喜欢用 [Mill](https://github.com/scottashipp/mill) 这样做，这是一个开源库，除了别的以外，它使用 Java 处理流更加优雅。通过在[工厂](https://github.com/scottashipp/mill)中调用`MoreCollectors.joining()`，前面的例子不需要额外映射到`Object::toString` :

```
import com.scottshipp.code.mill.stream.MoreCollectors;

// ...

return fruits.stream()
        .filter(Objects::nonNull)
        .collect(MoreCollectors.joining(", ")); 
```

Enter fullscreen mode Exit fullscreen mode

Mill 还以各种其他方式扩展了标准库产品。我想快速浏览一下其中的一些。

### 串联两个以上的流

两个流可以用 [Stream.concat](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/Stream.html#concat(java.util.stream.Stream,java.util.stream.Stream)) 连接起来。我一直想知道为什么他们不把它变成一个带有 varargs 的方法，允许我连接任意数量的流？

在[磨坊](https://github.com/scottashipp/mill)里，这样的方法是存在的。

```
Stream<Fruit> fruitInStore = StreamOps.concat(fruitInDeli, fruitInProduceSection, fruitOnEndCaps); 
```

Enter fullscreen mode Exit fullscreen mode

### 非空字符串

Java 标准库中的许多现有方法可以用作过滤流的谓词。例如，您可以在过滤器中使用`String::isEmpty`作为谓词。但是你为什么想要呢？普通代码中最常见的`String::isEmpty`用法是确保给定的字符串是*而不是空的*！

这促使这个令人沮丧的[堆栈溢出问题](https://stackoverflow.com/questions/21488056/how-to-negate-a-method-reference-predicate)显示 Java 8 甚至没有提供这样的东西。Java 9 或 10 也没有。

在 Java 11 标准库中，有一个笨拙的`Predicate.not`实用程序来获得对等物:

```
long nonEmptyStrings = s.filter(Predicate.not(String::isEmpty)).count(); 
```

Enter fullscreen mode Exit fullscreen mode

[然而，穆勒](https://github.com/scottashipp/mill)提供了这样一个谓词:

```
s.filter(StringPredicates.nonEmpty()); 
```

Enter fullscreen mode Exit fullscreen mode

### 通过比较过滤

另一个常见的用例是将列表过滤到给定的对象范围。继续我们的水果示例，假设我们只想列出以 A-H 开头的水果。

我所知道的使用标准库的最简单的方法是这样的(假设 moreFruits 是一个流):

```
moreFruits.stream()
    .filter(s -> String.CASE_INSENSITIVE_ORDER.compare(s, "h") <= 0)
    .collect(Collectors.joining(", "))); 
```

Enter fullscreen mode Exit fullscreen mode

这感觉相当笨拙，并且有一些认知开销，我们记得比较器如何返回整数，负数表示小于。

不如用[磨](https://github.com/scottashipp/mill)的方式？

```
moreFruits.stream()
    .filter(where(String.CASE_INSENSITIVE_ORDER).isLessThanOrEqualTo("h"))
    .collect(MoreCollectors.joining(", "))); 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的， [Mill](https://github.com/scottashipp/mill) 提供了一个 [fluent 接口](https://www.martinfowler.com/bliki/FluentInterface.html)来构建这种基于范围的过滤器。

## 但是，等等，还有更多

与其继续纠缠这一点，我会让你参考 Mill 的 Javadoc，在那里你可以找到更多关于这个库的内容。

如果您认为 Mill 可能对您有用，您会想要使用 [Jitpack](https://jitpack.io/) 将 [Mill](https://github.com/scottashipp/mill) 设置为您的应用程序的依赖项之一，这就像在您的 pom 中添加以下两个块一样简单:

**pom.xml**

```
<repositories>
    <repository>
      <id>jitpack.io</id>
      <url>https://jitpack.io</url>
    </repository>
  </repositories>

<dependencies>
    <dependency>
      <groupId>com.github.scottashipp</groupId>
      <artifactId>mill</artifactId>
      <version>v1.0</version>
    </dependency>

  </dependencies> 
```

Enter fullscreen mode Exit fullscreen mode

请参见 Jitpack 网站了解梯度方向。

### 贡献者

Mill 也对贡献者开放。例如，有一天通过 Maven Central 发布这个库可能会有意义。我计划很快发布一些我认为可能有用的东西。其中之一是一个字符串谓词，用于 not null *和* not empty。我不知道为什么我还没有包括这一点。另一个原因是，创建一个不带参数的 MoreCollectors.joining()并默认使用逗号分隔的列表可能有意义。

### 最后的话

如果你感兴趣，我还描述了 Mill 如何在“[更好的 Java 空检查](https://dev.to/scottshipp/better-null-checking-in-java-ngk)”中提供类似于空条件操作符的东西

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [斯科塔希普](https://github.com/scottashipp) / [米尔](https://github.com/scottashipp/mill)

### Java 库使一般的任务更加优雅。兼容 Java 8+。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 米尔 [![Build Status](img/927a371d5212f8a2d2b4c2741068f748.png)](https://travis-ci.com/scottashipp/mill)

[![logo](img/f5ce0956e0e330032bfeab8ac32eb16c.png)](https://github.com/scottashipp/millmedia/mill-rind-90x90.png?raw=true)

Java 库使一般的任务更加优雅。

### 注意:该库已被弃用。

2020 年 3 月更新。我将不再维护磨坊。现在有其他的库被更广泛的采用，有一些实际的资助和一组活跃的维护者。(参见 [StreamEx](https://github.com/amaembo/streamex) )。

另外， [Mill](https://github.com/lihaoyi/mill) 现在是 Java 和 Scala 的一个构建工具的名字。

尽管如此，它仍然有助于提供一些示例，您可以将这些示例添加到自己的代码库中，以改进 Java 流或 lambdas 的使用。

兼容 Java 8+。

## 在本自述文件中

*   [如何将 Mill 添加到您的应用程序中](https://github.com/scottashipp/mill#how-to-add-mill-to-your-application)
*   [一些例子](https://github.com/scottashipp/mill#some-examples)
*   [投稿指南](https://github.com/scottashipp/mill#contribution-guidelines)
*   [去哪里寻求帮助](https://github.com/scottashipp/mill#where-to-find-help)
*   [执照](https://github.com/scottashipp/mill#license)

## 如何将 Mill 添加到应用程序中

如果您正在使用 Maven 或其他支持的依赖管理工具，您可以使用 Jitpack 将 Mill 添加到您的应用程序中。

### 专家

首先，将 Jitpack(如果还没有)添加到存储库中…

</article>

[View on GitHub](https://github.com/scottashipp/mill)