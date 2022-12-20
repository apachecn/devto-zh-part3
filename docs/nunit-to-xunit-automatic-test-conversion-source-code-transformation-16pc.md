# NUnit 到 xUnit 的自动测试转换:源代码转换

> 原文：<https://dev.to/detunized/nunit-to-xunit-automatic-test-conversion-source-code-transformation-16pc>

在[上一篇文章](https://dev.to/detunized/nunit-to-xunit-automatic-test-conversion-pattern-match-5alb)中，我写了如何在代码中找到我想用简单的 C#语法重构的模式。基本上，我用一些通配符写出我想找到的精确表达式，这些通配符匹配不同的部分，其余的按原样匹配。有点像 regexp 或 shell 文件 glob。像这样:

```
Assert.That(_, Is.EqualTo(_))
Assert.That(_, Throws.TypeOf<_>()) 
```

或者

```
_._(_, _) 
```

如果你想走极端，用两个参数匹配每个成员函数调用。

不过，我希望能够做的是转换代码，而不仅仅是匹配。我想指定如何将模式转换成我想要的形式。例如:

```
Assert.That(@actual, Is.EqualTo(@expected)) -> Assert.Equal(@expected, @actual) 
```

这正是我要用的语法。占位符`@actual`和`@expected`匹配 AST 中的任何表达式子树，任何可以作为参数放在这两个位置的东西。它实际上是一个有效的 C#代码，因为它允许在标识符的开头使用`@`。在我的匹配器中，我将任何以`@`开头的标识符都视为占位符。

前一篇文章中的代码只匹配模式(`_`匹配任何东西，有点像正则表达式中的`.*`)。现在我们需要存储命名的匹配，就像正则表达式中的匹配组一样。代码必须从

```
// A placeholder matches anything
if (IsPlaceholder(pattern))
    return true; 
```

到

```
// A placeholder matches anything
if (IsPlaceholder(pattern))
{
    var name = pattern.ToFullString();
    if (name != "_")
        Matches[name] = code;

    return true;
} 
```

其余的匹配代码实际上保持不变。我们有了一个匹配器，下一步将是编写一个*" substitutor "*或 *"replacer"* ，即查找和替换工具的 *replace* 部分。

由于我喜欢让我的代码[保持干燥](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，首先我花了一堆时间思考如何重用替换部分的匹配代码。我失败了。所以我决定用[湿](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself#DRY_vs_WET_solutions)代替，重写那个巨大的开关一遍。

```
// _variables is populated in the constructor
public SyntaxNode Replace(SyntaxNode template)
{
    // A placeholder found. Substitute.
    if (IsPlaceholder(template))
    {
        var name = template.ToFullString();
        if (_variables.TryGetValue(name, out var v))
            return v;

        return template;
    }

    switch (template)
    {
    case ArgumentSyntax t:
        return t.Update(t.NameColon,
                        t.RefKindKeyword,
                        (ExpressionSyntax)Replace(t.Expression));
    case ArgumentListSyntax t:
        return t.Update(t.OpenParenToken,
                        Replace(t.Arguments),
                        t.CloseParenToken);
    case IdentifierNameSyntax t:
        return t.Update(Replace(t.Identifier));
    case InvocationExpressionSyntax t:
        return t.Update((ExpressionSyntax)Replace(t.Expression),
                        (ArgumentListSyntax)Replace(t.ArgumentList));
    case LiteralExpressionSyntax t:
        return t.Update(Replace(t.Token));
    case MemberAccessExpressionSyntax t:
        return t.Update((ExpressionSyntax)Replace(t.Expression),
                        t.OperatorToken,
                        (SimpleNameSyntax)Replace(t.Name));
    case GenericNameSyntax t:
        return t.Update(Replace(t.Identifier),
                        (TypeArgumentListSyntax)Replace(t.TypeArgumentList));
    case TypeArgumentListSyntax t:
        return t.Update(t.LessThanToken, Replace(t.Arguments), t.GreaterThanToken);
    default:
        return template;
    }
} 
```

这段代码递归地遍历替换模板 AST，并用上一步中找到的匹配替换占位符(如`@actual`和`@expected`)。然后，这个替换的最终结果与原始源文件 AST 中匹配的表达式节点交换。仅此而已。很管用。

> 这里有一个关于关键字选择的旁注。我不是 OOP 爱好者。我认为它有自己的位置，在 C#中使用类和成员函数来表示和做事情是很自然的。虽然大部分时间我都尽量坚持函数式风格，函数没有任何隐藏状态，将所有输入作为参数并返回结果。实际上，我的大多数函数和类都是静态的。但对我来说，常识在大多数时候都占了上风(至少我希望如此)。因此，在这种情况下，我使用了一个类和非静态函数来避免将状态传递给每个函数，因为有很多递归调用。从某种意义上说，我让它保持干燥。

我为这个算法写了一个小的驱动程序。它负责加载、解析、写出结果。目前所有的模式都是硬编码的，但是除了我的懒惰之外，没有什么能阻止我把它放到一个配置文件中。

以下是我用来转换大部分测试的模式:

```
Assert.That(@actual, Is.EqualTo(true))      -> Assert.True(@actual)
Assert.That(@actual, Is.EqualTo(false))     -> Assert.False(@actual)
Assert.That(@actual, Is.EqualTo(@expected)) -> Assert.Equal(@expected, @actual)
Assert.That(@code, Throws.TypeOf<@type>())  -> Assert.Throws<@type>(@code) 
```

## 结论

这里描述的解决方案不是一个任何人都可以捡起来使用的工作工具。这是一个碰巧有效的概念证明，具有价值。至少对我来说是这样。我能够跨几十个文件转换数百个测试，为自己节省了几个小时繁琐的手工劳动。我投入这个工具的时间没有回来。这里的纯 ROI 为负。但这不仅仅是时间的问题。我从学习新的东西中，从自动化一个烦人的任务中，从写博客和与世界分享中获得了足够的满足感。

代码可以在[这里](https://github.com/detunized/nunit2xunit)找到。

## 未来的工作

不是每个 AST 节点类型都被模式匹配代码覆盖，也不是每个可能的`Assert`表达式都被模式覆盖。这是可以扩展的。也许这也可以变成一个 Visual Studio[代码]扩展。一般来说，这不一定是一个单元测试转换工具。有了这个迷你 DSL，真正重构/转换任何代码都是可能的。也可以在代码库中搜索匹配项。很多想法，但没有那么多时间。

*原载于[detunized.net](https://detunized.net/posts/2019-03-26-nunit-to-xunit-automatic-test-conversion-source-code-transformation/)T3】*