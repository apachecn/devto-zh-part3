# 流畅的 API 让开发者喜欢使用你的库

> 原文：<https://dev.to/jamesmh/fluent-apis-make-developers-love-using-your-libraries-2d7c>

有没有发现自己对被认为有用的第三方库感到沮丧？好用吗？

你有没有回到几周前创建的好的/可重用的类，却发现你不知道如何开始使用它。没有什么明显突出的东西作为起点？

所以，你决定查阅文档，只是为了花一个小时试图弄清楚如何开始做你需要库做的一件事！

我去过那里很多次了！

在我的职业生涯中，我一直想知道是否有一种方法可以构建出 100%简单明了且易于使用的可重用部分。指导你正确使用的东西。

# 让我们向最好的学习

让我们试着向最优秀的人学习。最容易使用的工具。这里有几个我个人觉得很好用的工具:

*   PHP 框架
*   JQuery
*   。网络核心
*   LINQ

对我来说，这些代表了我职业生涯中遇到的一些最简单易用但功能强大的工具。

我不再使用 JQuery 了，也不会再使用了(是我自己选择的)。但是每个人都会同意，与当时的原生工具相比，JQuery 让使用 DOM 变得更加愉快。

那么，所有这些工具有什么共同点呢？是什么让它们如此易于使用？

它们是流畅的 API。

# 呃？什么是流畅的 API？

术语“流利”的含义是“流利的 API”在某种程度上与(口语)语言相关。例如，你可以说一口流利的英语。

流畅的界面是一种编程风格，它关注于你的代码如何读起来简单自然——就像你在读书一样。

# 可读性

例如，在。NET Core 你可能会发现这样的代码:

```
app.UseDeveloperExceptionPage()
    .UseDatabaseErrorPage(); 
```

Enter fullscreen mode Exit fullscreen mode

这很容易理解和阅读。您打算使用开发人员异常页面(它为开发人员提供了很好的调试信息)。当您遇到数据库错误时，您还希望使用一些特殊错误页面。

# 解锁新方法

再来看另一个例子:

```
services
    .AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，调用`AddMvc`方法解锁了对其他方法的访问，这些方法只在特定的上下文中有意义。

对我来说，这是流畅界面最强大的方面——它们只让你使用在当前环境下有意义的东西。

# 其他好处

当一个流畅的 API 做得很好时，你不应该能够:

*   以错误的顺序调用方法(曾经使用过这样的代码吗？)
*   不知道从哪里开始(因为 IDE intellisense 会告诉您有哪些方法可以“开始”)
*   部分调用特定链中的方法。(例如，“你忘了调用某个类的“Done”方法，所以它永远不会执行它的逻辑)

如果你想继续阅读这些想法，你可以阅读[这篇文章，在这篇文章中，我讨论了流畅的 API 给你带来的更多好处](https://www.blog.jamesmichaelhickey.com/3-benefits-fluent-interfaces/)。

# 应用于真实库

出于上述原因(以及我链接的帖子中的原因)，我选择在编写 [Coravel](https://github.com/jamesmh/coravel) 时使用流畅的 API。我认为这是人们使用它的主要原因之一——它很容易使用。

例如，您能理解这段代码的意图吗？

[![code](img/60a2b19757713ba83f7b60737cb453a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FBL9OMTb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cn9sq1d6m0rj2ce2r5iw.png)

这就是以流畅风格编写的代码是多么容易理解。这段代码的作用是显而易见的。

# 向导式代码完成

以我在 Visual Studio 代码中使用 Coravel 为例:

[![code](img/eff50fba80aa4a0c80a57715dcb4f597.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_o1sOgXa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nf1pagtvichhjzq53b3v.png)

当有人想安排某事时，他们可以使用新的方法。这些方法只在安排事情的上下文中可用。

这里没有其他上下文中的方法。

选择频率后，这些方法消失，您可以访问一组新的选项:

[![code](img/c3f94972b2ff2e563d0a1d1af666feac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RhipWMhw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ez8aafvn9erx9wfgick.png)

# 让我们现在就构建一个流畅的 API 吧！

所以你很想知道——我怎么才能造出一个呢？

构建基本的流畅 API 实际上非常简单。关键的“技巧”是，你的类中的方法可以返回一个接口，限制调用者可以使用什么方法。

下面是一个可行的例子:

```
public class MyFluentClass : ICalculation, IResult
{
  private int _value;
  private int _result;

  private MyFluentClass() { }

  public static ICalculation WithValue(int value)
  {
    return new MyFluentClass
    {
      _value = value
    };
  }

  public IResult Add(int toAdd)
  {
    this._result = this._value + toAdd;
    return this;
  }

  public IResult Subtract(int toSubtract)
  {
    this._result = this._value - toSubtract;
    return this;
  }

  public int Result() => this._result;
}

public interface ICalculation
{
  IResult Add(int toAdd);
  IResult Subtract(int toSubtract);
}

public interface IResult
{
  int Result();
} 
```

Enter fullscreen mode Exit fullscreen mode

注意事项:

*   使用私有构造函数，这样调用方就被迫使用静态工厂方法
*   静态工厂方法可以访问实例的私有成员
*   `WithValue`返回接口`ICalculation`。这就开始限制调用者可以使用的方法。
*   `Add`和`Subtract`返回界面`IResult`。这就是解锁链中的下一系列方法的原因(恰好是`Result`)。

这是它实际运行的样子:

[![1](img/4561523008574428fb5d802d1eacd215.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gkKFtIJk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bl28yhcpfbd39kije8yh.png)

[![2](img/bcbeaf2ae734cfedada6ae9f8632509a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vt68HL3u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/agxleb8xgwsjju50tc4c.png)

[![3](img/99373713e6ed8303b67658105aab8c70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LS47Yjec--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4gltpuw9b17jiv0xtjwn.png)

# 结论

感谢您花时间阅读本文——我希望您学到了一些新东西，并喜欢其中的内容！

顺便说一下，这是我的系列文章“到目前为止我在构建 Coravel 中学到的东西”的第 2 部分。以下是该系列的其他部分:

第 1 部分——到目前为止我所学到的构建 Coravel(开源。网芯工装)

这篇文章最初出现在 [builtwithdot.net 的博客](https://builtwithdot.net/blog/fluent-apis-make-developers-love-using-your-net-libraries)上。

# 导航您的软件开发生涯

一封电子邮件简讯，我将在其中回答订阅者的问题，并就以下主题提供建议:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？

听起来有趣吗？加入社区吧！