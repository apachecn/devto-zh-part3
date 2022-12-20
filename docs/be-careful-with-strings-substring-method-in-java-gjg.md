# 小心 Java 中 String 的 Substring 方法

> 原文：<https://dev.to/renegadecoder94/be-careful-with-strings-substring-method-in-java-gjg>

每隔一段时间，我都会遇到一个用编程语言编写的完善的库，它有自己的怪癖。作为一名教师，我必须确保在教学时意识到这些怪癖。例如，上一次[我谈了一点关于各种扫描仪输入方法](https://dev.to/renegadecoder94/be-careful-with-scanner-methods-in-java-460m)以及它们如何不以相同的方式运行。今天，我想谈谈来自 Java 字符串库的[子串方法。](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#substring(int,%20int))

## 文档

第一次使用库时，我发现查看文档很有用。但是对于这样一个已经建立的库来说，钻研文档有时感觉很愚蠢。毕竟很多语言都支持字符串。就我个人而言，我只需要知道命令的名称，然后就可以解决剩下的问题。

然而，每隔一段时间，我就会遇到一个不如我想象的直观的函数。这里我说的是 Java 的 substring 方法。你大概可以想象，它从一个字符串中抓取一个子串并返回它。那么，有什么条件呢？

首先，substring 方法实际上是一个重载方法。因此，在文档中，同一方法有两种不同的形式。看一看:

> 公共字符串 substring(int beginIndex)
> 
> 返回一个新字符串，它是该字符串的子字符串。子字符串从指定索引处的字符开始，一直延伸到该字符串的末尾。
> 
> Java API，2019

> 公共字符串 substring(int beginIndex，int endIndex)
> 
> 返回一个新字符串，它是该字符串的子字符串。子字符串从指定的`beginIndex`开始，延伸到索引`endIndex - 1`处的字符。因此子串的长度是`endIndex-beginIndex`。
> 
> Java API，2019

在这一点上，不要太在意他们的描述，因为我们会谈到这些。请注意，同一个方法有两个不同的版本。

## 用法

现在，我想花点时间展示如何使用 substring 方法。如果这是您第一次接触 Java API，这将是一个很好的机会。

首先，注意方法头不包含 static 关键字。换句话说，减法是一个有意义的实例方法。我们需要一个字符串的实例来获得子串:

```
String str = "Hello, World!";
String subOne = str.substring(7);
String subTwo = str.substring(0, 5); 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们创建了两个新的子字符串:一个从位置 7 到末尾，另一个从位置 0 到位置 5。不看文档，你能算出结果字符串是什么吗？

## 间隔符号

在我给出答案之前，我认为讨论一些数学术语是很重要的。特别是，我想谈谈音程记数法。

在区间表示法中，目标是明确说明某个子集的范围。例如，我们可能对所有大于 0 的整数感兴趣。在区间记数法中，这看起来像:

```
(0, +∞) 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们选择使用括号将值 0 排除在范围之外。我们可以很容易地定义从 1 开始的区间——注意括号:

```
[1, +∞) 
```

Enter fullscreen mode Exit fullscreen mode

无论哪种情况，我们描述的是同一个集合:所有大于 0 的整数。

那么，这与 substring 方法有什么联系呢？事实证明，子串是字符串的子集，所以我们可以使用区间符号来定义子串。为什么我们不举几个例子呢？鉴于“你好，世界！”，使用以下间隔确定子字符串:

*   [0, 2]
*   (0, 5]
*   (1, 3)
*   (-1, 7]

完成后，请查看以下答案:

*   “Hel”
*   “ello,”
*   " l "
*   “你好，W”

我们需要在前进的道路上保持这种想法。

## 真相

事实是子串方法有点奇怪。一方面，我们可以使用单个索引来指定新子串的起始点。另一方面，我们可以使用两个索引来获取字符串的任意子集。

但是，在实践中，我发现第二种选择给了很多学生麻烦，我不怪他们。毕竟，界限是有欺骗性的。例如，让我们重温上面的一些代码:

```
String str = "Hello, World!";
String subOne = str.substring(7);
String subTwo = str.substring(0, 5); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们可以很有信心地预测`subOne`的值为“世界！”，我们就对了。毕竟，索引 7 是“W”，该方法自动获取字符串的其余部分。

至于`subTwo`，我们可能会猜“你好”，但我们不会猜对。它实际上是“你好”，因为结束索引是唯一的(即[0，5])。在下一节中，我们将看看为什么会这样，以及我对此的感受。

## 我的拿

据我所知，包含/排除模型是 Java API 中范围的标准。也就是说，我确实偶尔会质疑设计选择。

一方面，可以使用字符串的长度作为子字符串的结束点是有好处的:

```
String jokerQuote = "Madness, as you know, is like gravity, all it takes is a little push.";
String newtonTheory = jokerQuote.substring(30, jokerQuote.length()); 
```

Enter fullscreen mode Exit fullscreen mode

但是，这真的有必要吗？Java 已经为 substring 方法提供了一个重载，可以准确地捕捉这种行为。

也就是说，这个符号有一个很好的数学解释，其中一部分与起点和终点之间的差异有关。特别地，我们得到新的子串的长度:

```
int length = endIndex - startIndex; 
```

Enter fullscreen mode Exit fullscreen mode

此外，这种特殊的符号允许相邻的子字符串共享一个中点:

```
String s = "Luck is great, but most of life is hard work.";
String whole = s.substring(0, s.length()/2) + s.substring(s.length()/2, s.length()); 
```

Enter fullscreen mode Exit fullscreen mode

这两个属性都很好，但我认为它们很可能是零索引的副产品([由 Dijkstra](https://www.cs.utexas.edu/users/EWD/transcriptions/EWD08xx/EWD831.html) 延续)，这也不完全是*直观的*。对于那些反对这个评论的人，请注意，我完全支持用零和这个包含/排除子集的约定来索引。

我想说的是，我看到我自己的学生被这两个大会绊倒，所以我在某种程度上同情他们。这就是为什么我一开始花了这么大力气写这篇文章。

如果你有同样的感觉或者我完全错了，请告诉我。否则，谢谢你花时间阅读我的作品。我希望你喜欢它！