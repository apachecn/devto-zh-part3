# Java 中不存在“else if”关键字

> 原文：<https://dev.to/renegadecoder94/the-else-if-keyword-doesnt-exist-in-java-1863>

听着，我知道这对你们中的一些人来说似乎很疯狂，但是关键字`else if`在 Java 中是不存在的。是啊，我今天 20 岁的时候也学会了。

## 社交媒体之美

如果你了解我，你就会知道[我讨厌社交媒体](https://therenegadecoder.com/blog/why-i-deleted-most-of-my-social-media/)。毕竟，我没有脸书或 Instagram，我尽量远离所有的聊天应用。不要让我加入你的 Slack、GroupMe、微信、Messenger 或 WhatsApp 群。在我尝试狂玩动漫的时候，我不需要任何其他方式让别人联系我。

也就是说，我确实经常使用 Twitter。大部分时间我用它来阅读曲棍球或了解最新的政治消息。然而，由于我每天通过 Twitter 消费的内容数量庞大，我偶尔会发现一两个金块。

最近，我看到马蒂亚斯·拜恩斯的一条推特，上面写道:

> 在 JS 中，有`if`和`else`，但是没有特殊的`else if`结构。看起来似乎有，因为`else if`工作……
> ……但它只是一个嵌套在`else`块中的`if`，没有花括号。
> else if(x){ }→else { if(x){ } }
> 知道的越多
> 
> -马蒂亚斯·拜恩斯，2019 年 1 月 31 日

这条推文让我大吃一惊，因为 if/else if/else 语法在我心中根深蒂固，我从未质疑过它。奇怪的是，我刚刚给自己的 Java 类教授了分支，我很高兴地解释了`else if`,就好像它是自己的关键字一样。我想这篇文章会发给他们。

## Java 中的适当分支

作为一名教师，我经常分两个阶段教授分支:基本分支，然后是嵌套分支。谁会想到它们是一回事呢？不服气？我们来看一个评分的例子:

```
Scanner in = new Scanner(System.in);
int grade = Integer.parseInt(in.nextLine());
if (grade >= 90) {
  System.out.println("You got an A!");
} else if (grade >= 80) {
  System.out.println("You got a B!");
} else if (grade >= 70) {
  System.out.println("You got a C!");
} else if (grade >= 60) {
  System.out.println("You got a D!");
} else {
  System.out.println("You got an F!");
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个简单的例子中，我们要求用户输入一个我们认为有效的数字。然后，我们针对五种情况测试这个数字，这样我们就可以输出适当的消息。

当然，我们可能应该看一个例子，假设一个用户输入值 75。一开始，我们会失败，因为 75 不大于或等于 90。因此，我们下一步检查 75 是否大于或等于 80。测试又一次失败了，所以我们下降到下一个我们最终通过的案例。此时，我们打印一条消息并退出到顶层。

如果您和我一样，这个实现不会让您感到惊讶。当我们想要相互依赖的条件时，我们创建一个`if`案例，一个`else`案例，以及尽可能多的`else if`案例。

## 只能有两个关键字

但是，如果我告诉你，`else if`关键字实际上在 Java 中并不存在呢？我知道这与我之前所说的直接矛盾，但这是真的。我们在上面的代码中缺少的只是大括号:

```
Scanner in = new Scanner(System.in);
int grade = Integer.parseInt(in.nextLine());
if (grade >= 90) {
  System.out.println("You got an A!");
} else {
  if (grade >= 80) {
    System.out.println("You got a B!");
  } else {
    if (grade >= 70) {
      System.out.println("You got a C!");
    } else {
      if (grade >= 60) {
        System.out.println("You got a D!");
      } else {
        System.out.println("You got an F!");
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有一组级联的`if`和`else`语句，而不是一系列简单的 if 语句。现在，我们可以看到每个 if 语句对下一个语句的依赖关系。

也就是说，看一个例子可能会有帮助，所以为了便于讨论，我们假设用户输入 75。不出所料，第一个测试用例会失败，因为 75 不大于或等于 90。结果，我们陷入了`else`的情况，我们正在测试 75 是否大于或等于 80。又不是，于是我们又一次陷入了`else`的说法。此时，我们满足`if`语句，打印我们的消息，并退出到顶层。

正如我们所看到的，两种解决方案的操作是相同的，这不应该太令人惊讶。毕竟，它们是等价的解决方案。

## 为后面的人

当我第一次发表这篇文章时，我收到了很多奇怪的反对，他们似乎误解了这篇文章的观点，所以我要为后面的人再说一遍:**这篇文章是关于 Java 语法的评论，它的目标读者是初学者**。

请尽量避免就以下话题对我说教的诱惑:

*   关键字可以有也可以没有空格
*   分支是好是坏(也就是你喜欢的控制流结构)
*   `else if`关键字在其他语言中是否存在
*   其他类 C 语言(即 JavaScript、C、C#、C++等)的相关性。)

还有，请尽量避免人身攻击。我知道这很难，但你必须控制自己。如果我的内容让你如此困扰，就不要看了。

## 神魂颠倒

如果你像我一样，`else if`不是一个单独的关键词的想法可能会困扰你很多。毕竟，这是我今天了解到的 subreddit 上的那些东西之一。我经历了近五年的教育和两年的行业，却没有意识到这一事实，这让我很惊讶。

老实说，这种想法无处不在，以至于一些语言确实有额外的控制流关键字。例如， [Python 有 elif 关键字](https://docs.python.org/3/tutorial/controlflow.html)。同时， [Ruby 有 elsif 关键字](https://ruby-doc.org/core-2.1.4/doc/syntax/control_expressions_rdoc.html)。而且，如果其他语言遵循同样的惯例，我也不会感到惊讶。

也就是说，你打算用你的新知识做什么？你和我一样疯狂吗？请在下面的评论中告诉我！谢谢你的来访。