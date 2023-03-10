# 用 C#扩展方法很容易

> 原文：<https://dev.to/dirkstrauss/extension-methods-are-easy-with-c-235d>

我以前说过很多次了，我喜欢使用扩展方法。它们可能是最容易编写的方法，可以前所未有地增强您的代码。如果你想了解更多，请继续阅读。

## 找别的？请尝试以下链接:

*   [C# 7 和。网芯食谱出版](https://dirkstrauss.com/c-7-net-core-cookbook-published/)
*   难以置信的强大多选功能。现在提高生产率
*   [使用简单技术的酷生活技巧](https://dirkstrauss.com/cool-life-hacks-using-simple-technology/)
*   [时髦的 Css——你需要知道的有价值的好处](https://dev.to/dirkstrauss/sassy-css--the-valuable-benefits-you-need-to-know-5447-temp-slug-6301632)
*   [与冒名顶替综合征作斗争？如何思考和克服](https://dirkstrauss.com/struggling-with-imposter-syndrome-think-overcome/)

如果你时间有点紧，去看看关于这个话题的视频讲解。

[https://www.youtube.com/embed/BKJGnmWcCVw](https://www.youtube.com/embed/BKJGnmWcCVw)

这将使您能够在自己的应用程序中快速使用扩展方法来增强代码。如果你想要更多，继续读下去。

## 扩展方法解释

如果您想在现有类型中添加方法，而不必重新编译代码或创建新的派生类型，那么扩展方法是一个不错的选择。

扩展方法是一种特殊类型的静态方法。然后在您扩展的类型上调用这些方法，就像它们是实例方法一样。

[![Extension Method displayed in IntelliSence](img/6f265aef2abd7f45f883f2f39493fb52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--acntPoOR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/dirkstrauss.com/wp-content/uploads/2019/05/IntelliSence.png%3Fw%3D640%26ssl%3D1)

深入看看解释扩展方法的[微软文档。考虑下面的代码清单:](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)

```
public static bool ToInt(this string value)
{
    return int.TryParse(value, out var _);
} 
```

这是一个典型的扩展方法，它作用于一个字符串类型，并检查该字符串是否可以被解析为一个有效的整数。请注意，TryParse 使用了一个 out var，它使用了一个 discards 变量。

提示:在 C# 7.0 中引入，丢弃是暂时的，虚拟变量是有意不用的。使用下划线(_)作为变量名来表示放弃。

## 使用扩展方法

为了使用您的扩展方法，您只需要像调用该类型的任何方法一样调用它。在我们的例子中，我们扩展了字符串类型。这在前面的代码清单中的扩展方法签名中可以看到。它使我们能够做到以下几点:

```
var sValue = "15";
if (sValue.ToInt())
{
    // Do something
} 
```

如果字符串变量可以成功解析为整数，那么扩展方法将返回 True。您甚至可以修改该方法以返回它所解析的整数，而不是布尔值。当然，您需要优雅地处理字符串值不能被解析为整数的情况。

## 获取源代码

如果您想获得该代码的工作示例，请转到下面的 GitHub repo，并将该 repo 克隆到您的本地机器上。

[dirkstrauss/extension methods demo](https://github.com/dirkstrauss/ExtensionMethodsDemo)

如果您有任何意见或建议，请随时在下面的评论区提出。

post [扩展方法很简单，C#](https://dirkstrauss.com/extension-methods-are-easy/) 最早出现在[编程和技术博客](https://dirkstrauss.com)上。