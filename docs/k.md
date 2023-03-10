# 有没有被 OO 语言文档中的<e>、<t>、<k v="">难倒过？</k></t></e>

> 原文：<https://dev.to/graphicbeacon/ever-been-stumped-by-e-t-k-v-in-oo-language-documentation-4hg4>

在 Reddit [/r/dartlang](https://reddit.com/r/dartlang) 群中，一个名叫 **NFC_TagsForDroid** 的人就导航 Dart 文档时的困惑联系了我。特别是在理解演示代码示例时使用的一些“标记”背后的含义时。

以下是用户评论的摘录:

> 你能写一篇关于如何阅读 dartlang 文档的解释吗？对一个初学者来说，大部分是没有意义的。比如:[https://API . dartlang . org/stable/2 . 1 . 0/Dart-core/List/add . html](https://api.dartlang.org/stable/2.1.0/dart-core/List/add.html)(Dart Dart:core List<E>添加抽象方法)
> 表头 List < E >添加抽象方法**什么是< E >** ？作为一个抽象方法，它会对什么产生影响呢？

用户正在引用列表类型的`add()`方法的签名:

```
void add(
  E value
); 
```

Enter fullscreen mode Exit fullscreen mode

混乱的源头是`E`。其他在各种文档代码示例中使用的有`T`、`K`和`V`。

## 那么这些是什么意思呢？

这些看似神奇的“标记字母”是用于表示所谓的*类型参数*的占位符*。这在*静态类型的*面向对象语言中很常见，并且在谈到**泛型**时会很明显。*

泛型提供了一种告诉编译器正在使用什么类型的方法，因此它知道要检查这一点。

换句话说如果你看到`<E>`读作**，那么`List<String>`就会读作【列表**。****

 ****现在来看一下，假设我们定义了一个`List<E>` :

```
List<String> fruits = ['apple', 'orange', 'pineapple']; 
```

Enter fullscreen mode Exit fullscreen mode

再看添加方法:

```
void add(
  E value
); 
```

Enter fullscreen mode Exit fullscreen mode

可以这样理解:`E`代表集合中的一个**元素**，无论我们最初在创建列表时指定的是什么类型**！在`fruits`的情况下其`String`。**

这是我们如何使用它:

```
fruits.add('mango');

fruits.add(1); // This will throw an error as it's the wrong type 
```

Enter fullscreen mode Exit fullscreen mode

## 那么为什么要使用特定的字母呢？

最简单的答案是*约定*。事实上，你可以使用任何你喜欢的字母，达到同样的效果。然而，常见的带有语义:

*   `T`意为一种类型
*   `E`是指一个元素(`List<E>`:元素列表)
*   `K`是键(在`Map<K, V>`中)
*   `V`是值(作为返回值或映射值)

即使我不使用上面的任何占位符字母，下面的代码也能工作。例如，请参见下面的片段:

```
class CacheItem<A> { // Using the letter A
  CacheItem(A this.itemToCache);

  final itemToCache;

  String get detail => '''
    Cached item: $itemToCache The item type is $A ''';
}

void main() {
  var cached = CacheItem<int>(1);
  print(cached.detail);
  // Output:
  // Cached item: 1
  // The item's type is int
} 
```

Enter fullscreen mode Exit fullscreen mode

→ [**跑在镖靶上**](https://dartpad.dartlang.org/b2f2b4b80a52b3e83e074b10086e7a87)

尽管使用的占位符是`A`，但这仍然有效。按照惯例，将使用`T`:

```
class CacheItem<T> {
  CacheItem(T this.itemToCache);

  final itemToCache;

  String get detail => '''
    Cached item: $itemToCache The item type is $T ''';
} 
```

Enter fullscreen mode Exit fullscreen mode

泛型非常强大，因为它们允许我们使用不同的类型重用同一个类

```
CacheItem<String> cachedString = CacheItem<String>('Hello, World!');
print(cachedString.detail);
// Output:
// Cached item: Hello, World!
// The item's type is String

var cachedInt = CacheItem<int>(30);
print(cachedInt.detail);
// Output:
// Cached item: 30
// The item's type is int

var cachedBool = CacheItem<bool>(true);
print(cachedBool.detail);
// Output:
// Cached item: true
// The item's type is bool 
```

Enter fullscreen mode Exit fullscreen mode

我希望这很有见地，你今天学到了一些新东西。

## 进一步阅读

*   [关于泛型的 StackOverflow 帖子](https://stackoverflow.com/questions/6008241/what-is-the-difference-between-e-and-t-for-java-generics)
*   [泛型语言指南](http://download.oracle.com/javase/1,5.0/docs/guide/language/generics.html)
*   [**免费飞镖截屏在 Egghead.io**](https://egghead.io/instructors/jermaine-oppong)

* * *

## 分享是关爱🤗

如果你喜欢读这篇文章，请通过各种社交渠道分享。此外，检查并 [**订阅我的 YouTube 频道**](https://youtube.com/c/CreativeBracket) (也点击铃铛图标)上的 Dart 视频。

[**订阅我的电子邮件简讯**](http://eepurl.com/gipQBX) 下载我的免费 35 页【Dart 入门电子书，并在新内容发布时得到通知。

**喜欢、分享和[关注我](https://twitter.com/creativ_bracket)T3】😍有关 Dart 的更多内容。******