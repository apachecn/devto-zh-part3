# 尾随逗号

> 原文：<https://dev.to/sckott/trailing-commas-5213>

先说尾随逗号(又名:“结尾逗号”、“悬空逗号”)。尾随逗号指的是数组或数组类对象中一系列值末尾的逗号，留下一个基本上为空的槽。例如`[1, 2, 3,]`

当我从事 Ruby 和 Python 项目时，我有点喜欢它们。已经指出了尾随逗号的许多优点，其中最常见的是 diffs:

```
diff --git a/hello.json b/hello.json
index e36ffac..d387a2f 100644
--- a/hello.json
+++ b/hello.json
@@ -1,4 +1,5 @@
 [
   "foo": 5,
   "bar": 6,
+ "apple": 7,
 ]
diff --git a/world.json b/world.json
index 14a2818..41f8a01 100644
--- a/world.json
+++ b/world.json
@@ -1,4 +1,5 @@
 [
   "foo": 5,
- "bar": 6
+ "bar": 6,
+ "apple": 7
 ] 
```

Enter fullscreen mode Exit fullscreen mode

关于该主题的博文示例:[https://dontkry.com/posts/code/trailing-commas.html](https://dontkry.com/posts/code/trailing-commas.html)，[https://medium . com/@ nikgraf/why-you-should-enforce-dangling-commas-for-multiline-statements-d 034 c 98 e 36 f 8](https://medium.com/@nikgraf/why-you-should-enforce-dangling-commas-for-multiline-statements-d034c98e36f8)

许多语言支持尾随逗号，有些语言甚至认为使用尾随逗号是最佳实践。

## 红宝石

```
["hello", "world"]
# => ["hello", "world"]
["hello", "world",]
# => ["hello", "world"] 
```

Enter fullscreen mode Exit fullscreen mode

对于散列也是如此。

## 巨蟒

```
["hello", "world"]
# Out[1]: ['hello', 'world'] ["hello", "world",]
# Out[2]: ['hello', 'world'] 
```

Enter fullscreen mode Exit fullscreen mode

对集合和字典同样有效。

## Javascript

Mozilla 全面概述了 Javascript 中的[结尾逗号。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Trailing_commas) 

```
["hello", "world"]
// ['hello', 'world']
["hello", "world",]
// ['hello', 'world'] 
```

Enter fullscreen mode Exit fullscreen mode

可能适用于其他数据类型…？

## 生锈

[https://users.rust-lang.org/t/trailing-commas/13993](https://users.rust-lang.org/t/trailing-commas/13993)

```
["hello", "world"]
// vs
["hello", "world",] 
```

Enter fullscreen mode Exit fullscreen mode

可能适用于其他数据类型…？

## 朱丽亚

[https://users.rust-lang.org/t/trailing-commas/13993](https://users.rust-lang.org/t/trailing-commas/13993)

```
( 1, 2 )
# (1, 2)
( 1, 2, )
# (1, 2) 
```

Enter fullscreen mode Exit fullscreen mode

对 Julia 中的数组也是如此。

## 其他

显然其他人也是如此:Perl、C#、Swift 等等…

## 不同意

有些人不喜欢尾随逗号:

*   尾随逗号:好的还是坏的做法？(TL；博士:很糟糕)

* * *

## R

然而，我做的主要开发工作是在 R 中，它不支持尾随逗号。

```
c("hello",  "world")  #> [1] "hello" "world  c("hello",  "world",  )  #> Error in c("hello", "world", ) : argument 3 is empty 
```

Enter fullscreen mode Exit fullscreen mode

一个警告是，你会在列表、向量、数据帧、矩阵的子集化过程中看到尾随的逗号，例如

```
mtcars[1:3,] 
```

Enter fullscreen mode Exit fullscreen mode

一个 blogger 提供了一个[覆盖来允许尾随逗号](http://r-de-jeu.blogspot.com/2013/03/r-and-last-comma.html)，尽管我认为这不是一个好主意，因为你可能不希望你自己的 R 控制台的行为与别人的完全不同。

我还没有看到任何关于 R 中尾随逗号作为一种语言特性的讨论，不管是好是坏。但并不意味着它不存在:)

## 哈斯克尔

和 R 一样，[不允许尾随逗号](https://www.joachim-breitner.de/blog/739-Avoid_the_dilemma_of_the_trailing_comma)！

而事实上，据称(我不用 Haskell):

> 因为添加到列表中比添加到列表前更常见，所以 Haskellers 开发了前导逗号的习语:

```
 ( foo
  , bar
  , baz
  , quux
  ) 
```

Enter fullscreen mode Exit fullscreen mode

## JSON

对许多人来说不幸的是 JSON [不允许尾随逗号](https://stackoverflow.com/questions/201782/can-you-use-a-trailing-comma-in-a-json-object?lq=1)

## 亦见:以逗号开头

*   [https://hacker noon . com/winning-arguments-with-data-leading-with-commas-in-SQL-672 B3 b 81 EAC 9](https://hackernoon.com/winning-arguments-with-data-leading-with-commas-in-sql-672b3b81eac9)
*   [https://gist.github.com/isaacs/357981](https://gist.github.com/isaacs/357981)
*   [https://community . r studio . com/t/leading-vs-trailing-commas-on-new-lines/6744/5](https://community.rstudio.com/t/leading-vs-trailing-commas-on-new-lines/6744/5)