# 榆树:从初学者到赢家

> 原文：<https://dev.to/rhg/elm-from-beginner-to-be-winner-51hc>

## 嘿那里！👋

我叫 Ryan，我是在 2016 年 9 月回家的火车上了解到 Elm 的。

从那以后，我完全被迷住了。

Elm 是一种用于构建 web 应用程序的编程语言。然而，它通常与 JS 框架(如 React 或 VueJS)相比较，因为它提供了一个很好的向上扩展的模型！

学习 Elm 教会了我一种更简单的方法来做我以前正在做的事情，我认为这是值得分享的东西！

## dat 语法 tho😯

如果你和我一样，你以前见过 Java、C++、C#或 JavaScript 代码。

尽管每种语言都有其独特的品质，但语法却非常相似:

```
// Java
public static int add(int a, int b)
{
    return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// C++
public static int add(int a, int b)
{
    return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// C#
public static int Add(int a, int b)
{
    return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// JavaScript
function add (a, b) {
    return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

我永远忘不了第一次看 C#，来自 Java 背景。我很欣慰地发现我所有的知识都被翻译了，他们只是想大写他们的方法名😅

话虽如此，我也永远不会忘记我第一次看《榆树代码》的时候:

```
add a b =
    a + b 
```

Enter fullscreen mode Exit fullscreen mode

我差点死掉。

为了让你的经历不那么致命，我想**逐渐**向你介绍 Elm 代码，这样你就不会逃避它(就像我差点做的那样)！

我要给你看的是:

1.  JavaScript 中的一些代码。
2.  Elm 中的等效代码。
3.  有什么不同就评论一下！

我甚至会在标题中使用表情符号，这样你就知道该感受哪种情绪了😌

### 字符串😀

```
"Ryan" 
```

Enter fullscreen mode Exit fullscreen mode

```
"Ryan" 
```

Enter fullscreen mode Exit fullscreen mode

好吧，弦乐很简单...

### 数字😁

```
123 
2.5 
```

Enter fullscreen mode Exit fullscreen mode

```
123
2.5 
```

Enter fullscreen mode Exit fullscreen mode

和数字看起来一样。

### 布尔🙂

```
true
false 
```

Enter fullscreen mode Exit fullscreen mode

```
True
False 
```

Enter fullscreen mode Exit fullscreen mode

他们大写了我们的布尔！

### 变量🤔

```
var name = "Ryan"
var year = 2019
var cool = false 
```

Enter fullscreen mode Exit fullscreen mode

```
name = "Ryan"
year = 2019
cool = False 
```

Enter fullscreen mode Exit fullscreen mode

Elm 不使用`var`，只是给一个值赋一个名字！

(另外，无论你是否输入关键字，我都不觉得酷...)

### 列表😃

```
[ 1, 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

```
[ 1, 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

你可以像以前一样列清单！

### 物体🤔

```
{ name: "Ryan", year: 2019 } 
```

Enter fullscreen mode Exit fullscreen mode

```
{ name = "Ryan", year = 2019 } 
```

Enter fullscreen mode Exit fullscreen mode

对于 Elm，我们使用`=`而不是`:`，这与我们之前看到的变量是一致的。

### 功能😐

```
function add (a, b) {
  return a + b
} 
```

Enter fullscreen mode Exit fullscreen mode

```
add a b =
    a + b 
```

Enter fullscreen mode Exit fullscreen mode

好吧，让我们来分析一下这个坏小子:

1.  我们不需要逗号或括号作为输入，我们可以使用空格来分隔参数。
2.  就像 eariler 用`var`一样，Elm 不需要你输入`function`。它知道你想要一个函数，因为你在`=`之前给了它参数！
3.  elm 中的每个函数都返回值，所以不需要把它打出来。这个函数会返回`a + b`(我保证)

公平地说，最新的 JavaScript 实际上看起来更像带有箭头功能的 Elm:

```
var add = (a, b) => a + b 
```

Enter fullscreen mode Exit fullscreen mode

## 还感兴趣？🙄

如果你还在读这篇文章，那么希望你对如何用这种语言取代 HTML、JS 和 CSS 感到好奇！

那样的话，你应该在这里查看一下官方指南:
[https://guide.elm-lang.org](https://guide.elm-lang.org)

记住:你能做到！此外-----------------------------------------------------------------《♥️》的每一步都将得到你的支持

**想让*我*告诉你更多吗？请告诉我！我总是在业余时间给陌生人洗脑。**

感谢阅读！🥰