# 仙丹的如果和仙丹的做

> 原文：<https://dev.to/edisonywh/elixirs-if-and-elixirs-do-jol>

所以最近我发现了一个事实，即 Elixir 充满了宏，它允许你很容易地与它的内部语法树进行交互(使用`quote`)，我认为这是一件很酷的事情。

> 用最简单的术语来说，宏是特殊的函数，用于返回一个引用的表达式，该表达式将被插入到我们的应用程序代码中。- [仙丹郎](https://elixirschool.com/en/lessons/advanced/metaprogramming/#macros)

我遇到的一个例子是这样的——假设我们有一个表达式需要求值:

```
if true do
  "hello world"
end 
```

Enter fullscreen mode Exit fullscreen mode

但有时你想写一个单行表达式。没问题，朋友，长生不老药会保护你！

```
if true, do: "hello world" 
```

Enter fullscreen mode Exit fullscreen mode

这句话只有一行，但它让我兴奋到足以写一篇关于它的帖子。我们来深究一下为什么！

在这个简单的表达式中，有三点需要注意。

*   `do`前有个**逗号**
**   后有一个**冒号** *做**   有**没有**有`end`关键字*

 *这三个是让我大吃一惊的提示。

你刚才看到的`if`声明？它其实不是一个语言关键字，只是一个普通的函数(其实就是一个宏)。

你刚才看到的那个`do`块？也不是一个*语言*关键词，它是一个关键词列表(从技术上讲，根据[源代码](https://github.com/elixir-lang/elixir/blob/master/lib/elixir/lib/macro.ex#L839)，它是一个关键词列表关键词)。如果你不知道什么是关键词列表，请继续阅读，我会进一步解释。

[![img](img/3dfb78dba3b9da0059b45b181344ef2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yuQxZMZI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thumbs.gfycat.com/KlutzyIndolentGander-max-1mb.gif)

## 这些提示有什么帮助？

让我们重温这些提示，看看这些提示如何帮助我们揭开一直隐藏在众目睽睽之下的真相！

### ①逗号

如果你以前注意过，第一个提示指出`true`和`do`语句是分开的。

你知道还有什么是分开的吗？**函数参数**。

`if`语句实际上只是一个 arity 为 2 的函数，首先是要评估的条件，其次是要执行的操作，**逗号分隔两个参数**。

这意味着我们可以把前面的语句翻译成这样，它仍然有效。

```
iex(2)> if(true, do: "hello world")
"hello world" 
```

Enter fullscreen mode Exit fullscreen mode

我所做的只是在它周围加了括号——它们现在看起来是不是更熟悉了？；)

### 2)冒号

第二个和第三个提示其实关系非常密切，因为它们都指向一件事:**关键词列表**。

让我们回顾一下什么是关键字列表，或者它看起来像什么。

关于`Keyword List`的官方定义

> 关键字是二元元组的列表，其中元组的第一个元素是原子，第二个元素可以是任何值。——[仙丹郎](https://hexdocs.pm/elixir/Keyword.html)

我们知道列表是什么样子的-> `[]`

我们知道一个元组是什么样子的-> `{}`

所以，一个关键词列表看起来是这样的:

```
[{:atom, "any_value"}] 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这看起来不太好/不容易打出来，所以 Elixir 提供了一个语法糖来写这样一个关键字列表:

```
[atom: "any_value"] 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们可以使用这个新发现的逻辑来应用回我们之前的`if`语句，这就转化为:

```
iex(2)> if(true, [do: "hello world"])
"hello world" 
```

Enter fullscreen mode Exit fullscreen mode

同样，我在这里所做的只是在关键字`do`周围加了方括号，并且开始弄清楚它们是什么。

事实上，我们可以用关键字列表的原始语法变得更疯狂一些..

```
iex(2)> if(true, [{:do, "hello world"}])
"hello world" 
```

Enter fullscreen mode Exit fullscreen mode

*万岁，仍然有效！*

### 3)`end`在哪里？(并证明时间！)

好吧，我已经告诉你它们是一样的东西，它们都完全一样，但就你而言，我可能是在胡说八道(*尽管我保证我不是*)。所以让我们证明我说的是对的！

我还没有告诉你第三个提示，那就是——`end`关键字去了哪里？

所以，`end`实际上被完全忽略了，因为没有必要在抽象 Synax 树中表示语句的`end`——您应该能够从语法树本身推断出它。稍后，我还在 Elixir 的源代码中展示了这方面的证据。

我不会过多地谈论 AST，部分原因是它本身是一个完全不同的话题，但也是因为说实话我对它还不够了解😬)但是快速入门/目前为止我所理解的是:

> AST 是机器理解代码流的方式。它基于编程语言的语法和机器执行应该如何流动来构建一棵树。

本质上，**你写一段文字(代码)，语言试图理解它(解析)，然后它创建一个它当前理解的心智模型(抽象语法树)。**

我之所以运行不同的表达式来产生 AST 以证明它们是相同的，是因为代码的相同性不依赖于任何语法规则，而是语言真正从中理解了什么，在我们的例子中，这是语言的心理模型(AST)。

时间验证！

```
first = quote do
  if true do
    "hello world"
  end
end
# {:if, [context: Elixir, import: Kernel], [true, [do: "hello world"]]}

second = quote do
  if true, do: "hello world"
end
# {:if, [context: Elixir, import: Kernel], [true, [do: "hello world"]]}

third = quote do
  if(true, do: "hello world")
end
# {:if, [context: Elixir, import: Kernel], [true, [do: "hello world"]]}

fourth = quote do
  if(true, [do: "hello world"])
end
# {:if, [context: Elixir, import: Kernel], [true, [do: "hello world"]]}

fifth = quote do
  if(true, [{:do, "hello world"}])
end
# {:if, [context: Elixir, import: Kernel], [true, [do: "hello world"]]} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以运行最终检查来检查它们是否彼此相等！

```
first  == second #=> true
second == third  #=> true
third  == fourth #=> true
fourth == fifth  #=> true 
```

Enter fullscreen mode Exit fullscreen mode

坦率地说，我不知道 Elixir 实际上如何决定继续读取/解析语句，直到遇到一个`end`为止，我感觉有一个关于词法分析、解析和标记化的全新主题需要我首先理解(还有 AST，我会理解，但还不是时候！)

但是！我可以添加的来支持这篇文章的证据是，当 Elixir 将 AST 作为一个字符串(带有`Macro.to_string/1`)输出时，它们必须将`end`单词显式附加到输出的字符串中，如这里所见的！

## 外卖

```
if true, do: "hello world" 
```

Enter fullscreen mode Exit fullscreen mode

我从这一行简单的代码中学到了两件事，那就是:

*   `if`只是一个语言宏(它扩展成了一个`case`语句)，事实上，在 Elixir 的世界里有一个关于 meter programming/macros 的全新世界！
*   `do`无非是关键词列表。

我曾认为像`if`语句或声明函数这样的基本东西应该是具有特殊含义的核心语言特性，但是 Elixir 能够很好地构建像 AST/keyword list 这样的基本特性，并使用它来构建其他特性。

这也意味着你可以构建任何你想要的宏！想在 Ruby 中构建一个`unless`特性吗？可以这么做(如图[仙丹官方指南](https://elixir-lang.org/getting-started/meta/macros.html#our-first-macro))，想建一个`while`循环？，你也可以做到这一点(Chris McCord 在他的[元编程药剂](https://www.youtube.com/watch?v=2Bjzml_Hpvk)介绍视频中展示了这一点)。

实际上....让我留给你一件事。`def`本身就是一个宏，用`defmacro`定义。而`defmacro`本身就是一个宏，用`defmacro`定义。惊呆了吗？如果没有，请随意重读这句话，直到你做到了。[点击此处阅读更多](https://elixirschool.com/en/lessons/advanced/metaprogramming/)。

## 作者注

我可能忽略了很多细节(特别是关于 AST 的)，因为坦率地说，我还没有很好地理解它们(本文的重点并不真正在 AST 上，它们只是证明它们在没有语法糖的情况下都是平等的一种手段)。

但这意味着我确切地知道我接下来要钻研什么，我迫不及待地想了解更多关于词法分析器、语法分析器、标记器和语法分析器的知识:)如果你有关于它们的任何好的资源，请在⬇️下面评论，我会非常感谢它们的！

页（page 的缩写）我从仙丹松弛频道[了解到这一点，在这里](https://elixir-slackin.herokuapp.com/)，**马丁·斯瓦林**解释说它们都是等价的，我决定更深入一点，探索 AST 以验证他所说的。*剧透:他是对的！**