# 回顾 C# 7:抛出表达式

> 原文：<https://dev.to/csmacnz/looking-back-on-c-7-throw-expressions-2cc>

[![Looking Back on C# 7: throw expressions](img/4566ebb14f8e59bb44e657ef6b795806.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TI-DMmkR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1530226406379-f84b9edd291b%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

随着 C# 8 的到来，我想这是一个很好的时机来反思一下这种语言最近的新增内容。有些很棒的改进你可能错过了，有些我真的很喜欢使用，有些我认为已经达到了规范使用状态，我认为这些都值得反思。

抛出异常在 C#中既是一件好事也是一件坏事。你可以从任何地方抛出来展开堆栈。但是添加异常抛出和处理代码可能会导致开销，您需要对此保持警惕。更不用说用控制流使代码更难推理了。

不管怎样，我们有它们，我们使用它们，所以语言应该尽可能地使之容易。在 C# 7 中，这得到了增强，支持使用`throw`作为表达式。到目前为止，它始终是一个语句，这意味着代码中有 throw 不能进入的地方。

一个只支持表达式而不支持语句的新语言特性是 Elvis 操作符(或空条件操作符，我们在中已经回顾过了),另一个是空合并操作符。当涉及到验证代码时，这两个特性一起闪耀。

```
public MyClass(string firstArg, string secondArg, Widget widget)
{
    if(firstArg is null)
    {
        throw new ArgumentNullException(nameof(firstArg));
    }
    if(secondArg is null)
    {
        throw new ArgumentNullException(nameof(secondArg));
    }
    if(widget is null)
    {
        throw new ArgumentNullException(nameof(widget));
    }

    _firstArg = firstArg;
    _secondArgLength = secondArg.Length;
    _widget = widget;
} 
```

好啰嗦！把它折叠起来不是很好吗？使用 throw 表达式，我们可以。

```
public MyClass(string firstArg, string secondArg, Widget widget)
{
    _firstArg = firstArg ?? throw new ArgumentNullException(nameof(firstArg));
    _secondArg = secondArg?.Length ?? throw new ArgumentNullException(nameof(secondArg));
    _widget = widget ?? throw new ArgumentNullException(nameof(widget));
} 
```

所以更短更简洁。这样节省了打字时间和阅读时间。神奇的是，空合并操作符的右边必须有一个计算结果为正确类型的表达式。因为 throw 现在是一个表达式，它完全可以做到这一点，并满足编译。

还记得我们如何看待 C# 6 中的[表达式体成员](https://blog.csmac.nz/looking-back-on-csharp6-expression-body-members)和 c# 7 中的[更多吗？顾名思义，身体*是*的一种表达方式。现在我们有了投掷表达式，身体也可以使用它们了！](https://blog.csmac.nz/looking-back-on-csharp7-throw-expressions/looking-back-on-csharp7-more-expression-body-members) 

```
public class Widget
{
    public Widget() => throw new ArgumentException("Requires arguments, none provided.");

    public ~Widget() => throw new NotSupportedException("What are you building that needs a destructor, anyway? (Just curious.)");

    public string MyValueProperty => throw new NotImplementedException();

    public decimal CalculateTheAnswer() => throw new NotImplementedException();

    private T[] _items = new T[100];
    public T this[int i]
    {
        get => _items?[i] ?? throw new Exception("Not initialised somehow.");
        set => throw new NotSupportedException("An interface made me do this.");
    }
} 
```

还有其他的用例，表达式到处都在使用。但是我将把它作为一个练习留给读者自己去发现。