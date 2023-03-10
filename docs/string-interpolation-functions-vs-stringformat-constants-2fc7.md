# 字符串插值函数与字符串。格式常数

> 原文：<https://dev.to/scotthannen/string-interpolation-functions-vs-stringformat-constants-2fc7>

你曾经这样做过吗，也许是为了构造一个 URL 或者类似的东西？我有:

```
const string SomethingFormat = "I need to interpolate {0}, {1}, and {2}";

string GetInterpolatedString(string value1, DateTime value2, int value3)
{
    return string.Format(SomethingFormat, value1, value2, value3);
} 
```

如果我们将格式字符串分离为一个常量，这表明

*   我们在多个地方使用了它，不想重复它
*   出于可读性的考虑，我们将其分离出来

在使用字符串插值代替`string.Format`时，我们如何这样使用字符串常量？

简而言之，我们没有，因为我们不能。也许这不是最好的方法。

这不会被编译，因为该值不是一个常数:

```
const string SomethingFormat = $"I need to interpolate {value1}, {value2}, and {value3}"; 
```

这不会被编译，因为，嗯，它没有任何意义:

```
static string SomethingFormat = $"I need to interpolate {value1}, {value2}, and {value3}"; 
```

潜在的问题是，我们试图创建的根本不是一个常量，而是一个函数——在本例中，这个函数有三个参数并返回一个`string`。

一开始展示的“模式”是有效的，但是有严重的缺陷。为什么？因为我们可以这样做:

```
const string SomethingFormat = "I need to interpolate {0}, {1}, and {2} ... and {3}!";

string GetInterpolatedString(string value0, DateTime value1, int value2)
{
    return string.Format(SomethingFormat, value0, value1, value2);
} 
```

...它编译了，但是给出了一个运行时错误，因为我们的常量需要四个参数，但是我们只提供了三个。我们认为将`string`存储为常量是聪明的(好吧，我是这么做的)，但是它与使用它的函数的断开使它变得脆弱。当然，我可以写一个单元测试，但是字符串插值更好，因为除了更容易读写之外，当它出错时，它给我们的是编译器错误而不是运行时错误。

如果常量在很多地方被使用，这也变得更难理解，这意味着当我们修改使用它的方法时，我们看不到常量，反之亦然。

再说一遍，我们真正要完成的不是一个常数，而是一个函数。

实现这一点的一种方法是用一个函数来代替我们的常数，可能是这样的:

```
static string FormatSomething(string foo, int bar, DateTime date)
{
    return $"I need to interpolate {foo}, {bar}, and {date}";
} 
```

那好多了，但是仍然有一个问题。如果这个函数的使用不局限于一个类或方法，而我改变了它，那该怎么办？是的，我会得到一个编译器错误，这很好，但是编译器错误可能会出现在错误的地方*。
这个函数有点像实现接口的类。它的存在是为了服务于消费者的特定目的。
如果我改变它，也许是通过添加或删除参数，编译器错误将表明它的消费者用错误的参数调用它。
但事实并非如此。我实际上已经破坏了这个函数，使它不再满足消费者的需求。*

 *我真正想要的是函数上的编译器错误，表明它的签名是错误的，就像我在
一个类实现了一个接口但没有正确的方法或签名时得到的一样。

我们如何指定一个方法，而不是一个类，实现一个期望的签名？委托人:

```
delegate string FormatSomethingFunction(string foo, int bar, DateTime date); 
```

实现如下所示:

```
static FormatSomethingFunction FormatSomething { get; } = (foo, bar, date) => 
    $"I need to interpolate {foo}, {bar}, and {date}"; 
```

正如类实现接口一样，该方法被明确要求符合委托的签名。

正如我所提到的，我最近意识到，多年来，当我需要创建一个抽象来为一个类提供一个方法时，我几乎总是创建一个接口和一个类实现，尽管我所需要的只是一个方法。老实说，
我从未考虑过使用委托，尽管它们的存在正是为了这个目的——为方法创建抽象。
而且，令我惊讶的是，我发现很多时候使用更具体的工具[会带来意想不到的好处](http://scotthannen.org/blog/2018/03/18/depending-on-functions-instead-of-interfaces.html)。

为什么不是这个？

```
static Func<string, int, DateTime, string> FormatSomething { get; } = (foo, bar, date) => 
    $"I need to interpolate {foo}, {bar}, and {date}"; 
```

如果我们只使用方法签名而不是委托，那么函数的预期用途就不清楚了。我们可以拥有用于完全不相关目的的函数，这些函数具有完全相同的签名。使用一个委托并给它命名(清楚地)不仅能传达它的签名，还能传达它的目的。

最初的问题似乎太小了，不能称之为问题。但是我们已经完成的是:

1.  我们利用了强类型，因此运行时错误被编译器错误所取代。格式字符串和它的参数之间没有编译时关系。字符串插值改进了这一点，所以我们正在利用它。
2.  我们尽可能明确地声明代码的意图，以便未来的开发人员能够容易地理解他们将要修改的内容。(我会说，“修改或调试”，但前面的观点使得调试不太可能。)

它只比一个`string.Format`常量可读性强一点点，也更容易理解，所以可读性不是最大的好处。
更有可能节省的是人们没有花时间去弄清楚为什么他们所做的改变会破坏某些东西。*