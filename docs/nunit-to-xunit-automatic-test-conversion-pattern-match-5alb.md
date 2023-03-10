# NUnit 到 xUnit 自动测试转换:模式匹配

> 原文：<https://dev.to/detunized/nunit-to-xunit-automatic-test-conversion-pattern-match-5alb>

在[之前的帖子](https://dev.to/detunized/nunit-to-xunit-automatic-test-conversion-3hc6)中，我描述了如何使用 Roslyn API 在 C# AST 中查找代码模式，以及如何更改 AST 以将原始代码重写为其他代码。目标是自动化 NUnit 测试到 xUnit 的转换。我使用的方法非常繁琐，因为我必须编写一个很长的链或 if 和 types 来完成这项工作。让我们这次努力做得更好。让我们从搜索和替换工具中的搜索部分开始。

最好能够指定这样的结构模式:

```
Assert.That(_, Is.EqualTo(_))
Assert.That(_, Is.EqualTo(true))
Assert.That(_, Is.Throws.TypeOf<_>()) 
```

它们将与实际代码相匹配:

```
// Matched by 'Assert.That(_, Is.EqualTo(_))'
Assert.That(account.Id, Is.EqualTo(id))
Assert.That("".ToBytes(), Is.EqualTo(new byte[] {}))

// Matched by 'Assert.That(_, Is.EqualTo(true))'
Assert.That(info.IsMd5, Is.EqualTo(true));
Assert.That(token.BoolAt(path, true), Is.EqualTo(true));

// Matched by 'Assert.That(_, Is.Throws.TypeOf<_>())'
Assert.That(() => Quad[-1], Throws.TypeOf<ArgumentOutOfRangeException>())
Assert.That(() => access(token, path), Throws.TypeOf<JTokenAccessException>()) 
```

乍一看，这似乎是一项相当困难的任务。但事实证明，它的简单形式并没有那么难。当我为用[罗斯林报价器](https://roslynquoter.azurewebsites.net/)替换 AST 生成代码时，我第一次有了这个想法。看着它的源代码，我发现了一堆 [`SyntaxFactory`类](https://docs.microsoft.com/en-us/dotnet/api/microsoft.codeanalysis.csharp.syntaxfactory?view=roslyn-dotnet)的`Parse*`方法。

所以基本上一个函数调用将解析代码片段并返回给定模式的 AST:

```
var patternAst = SyntaxFactory.ParseExpression("Assert.That(_, Is.EqualTo(_))"); 
```

上面的一行代码相当于这样一堵墙:

```
var patternAst =
    InvocationExpression(
        MemberAccessExpression(
            SyntaxKind.SimpleMemberAccessExpression,
            IdentifierName("Assert"),
            IdentifierName("That")))
    .WithArgumentList(
        ArgumentList(
            SeparatedList<ArgumentSyntax>(
                new SyntaxNodeOrToken[]{
                    Argument(
                        IdentifierName("_")),
                    Token(SyntaxKind.CommaToken),
                    Argument(
                        InvocationExpression(
                            MemberAccessExpression(
                                SyntaxKind.SimpleMemberAccessExpression,
                                IdentifierName("Is"),
                                IdentifierName("EqualTo")))
                        .WithArgumentList(
                            ArgumentList(
                                SingletonSeparatedList<ArgumentSyntax>(
                                    Argument(
                                        IdentifierName("_"))))))}))); 
```

感觉已经是完胜了，我们甚至还没有做任何有用的事情。但是让我们在一个源 AST 中找到这个模式。首先，我们需要解析我们正在搜索的文件:

```
var sourceAst = CSharpSyntaxTree.ParseText(File.ReadAllText(filename)); 
```

这为我们提供了 AST 中所有表达式节点的列表:

```
var nodes = sourceAst.GetRoot().DescendantNodes().OfType<ExpressionSyntax>(); 
```

现在我们找到匹配的节点:

```
foreach (var e in nodes)
{
    if (Ast.Match(e, patternAst))
    {
        var line = e.GetLocation().GetLineSpan().StartLinePosition.Line;
        var code = e.NormalizeWhitespace();
        Console.WriteLine($" {line}: {code}");
    }
} 
```

显然`Ast.Match`函数是比较棘手的一个。但实际上没那么棘手。我们并行递归遍历这两个 ast，看看它们是否匹配:

```
public bool Match(SyntaxNode code, SyntaxNode pattern)
{
    // A placeholder matches anything
    if (IsPlaceholder(pattern))
        return true;

    // Node types don't match. Clearly not a match.
    if (code.GetType() != pattern.GetType())
        return false;

    switch (code)
    {
    case ArgumentSyntax c:
        {
            var p = (ArgumentSyntax)pattern;
            return Match(c.Expression, p.Expression);
        }
    case ArgumentListSyntax c:
        {
            var p = (ArgumentListSyntax)pattern;
            return Match(c.OpenParenToken, p.OpenParenToken)
                && Match(c.Arguments, p.Arguments)
                && Match(c.CloseParenToken, p.CloseParenToken);
        }
    case IdentifierNameSyntax c:
        {
            var p = (IdentifierNameSyntax)pattern;
            return Match(c.Identifier, p.Identifier);
        }
    case InvocationExpressionSyntax c:
        {
            var p = (InvocationExpressionSyntax)pattern;
            return Match(c.Expression, p.Expression)
                && Match(c.ArgumentList, p.ArgumentList);
        }
    case LiteralExpressionSyntax c:
        {
            var p = (LiteralExpressionSyntax)pattern;
            return Match(c.Token, p.Token);
        }
    case MemberAccessExpressionSyntax c:
        {
            var p = (MemberAccessExpressionSyntax)pattern;
            return Match(c.Expression, p.Expression)
                && Match(c.Name, p.Name);
        }
    case GenericNameSyntax c:
        {
            var p = (GenericNameSyntax)pattern;
            return Match(c.Identifier, p.Identifier)
                && Match(c.TypeArgumentList, p.TypeArgumentList);
        }
    case TypeArgumentListSyntax c:
        {
            var p = (TypeArgumentListSyntax)pattern;
            return Match(c.LessThanToken, p.LessThanToken)
                && Match(c.Arguments, p.Arguments)
                && Match(c.GreaterThanToken, p.GreaterThanToken);
        }
    default:
        return false;
    }
} 
```

所以它基本上是一个巨大的交换机，每个节点类型都在其中。到目前为止，这里没有涵盖所有类型，只是我需要让我的例子工作。我想，要涵盖大部分 C#语法，我必须冗长地写几千行重复的代码。我不会在短时间内做完所有的事情。正是我需要的覆盖用例的东西。

通过添加几行代码，这已经成为在代码库中搜索代码模式的有用工具。下次我们看看如何实现替换部分。目标是重构，而不仅仅是搜索，不是吗？我对如何做这件事有些想法。下次见。

## 结论

感谢罗斯林令人敬畏的 API，它只有 [172 行代码](https://gist.github.com/detunized/d02a0640986f44243dc01e5f50f42e74)，我们有了一个非常先进的代码 grep。当然，目前它只是一个玩具和概念验证。要想让它变得更好，需要付出巨大的努力。但是我很高兴用这么少的努力就能做到。太神奇了。

*原载于[detunized.net](https://detunized.net/posts/2019-03-16-nunit-to-xunit-automatic-test-conversion-pattern-match/)T3】*