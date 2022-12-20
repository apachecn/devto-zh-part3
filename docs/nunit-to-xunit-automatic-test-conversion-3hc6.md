# NUnit 到 xUnit 自动测试转换

> 原文：<https://dev.to/detunized/nunit-to-xunit-automatic-test-conversion-3hc6>

我目前正在对一个有很多 NUnit 测试的 C#库进行重大重构。在没有任何充分理由的情况下，我决定将它们迁移到 xUnit 是一个好主意。我手工做了一些，结果很乏味。真的很乏味。最常见的模式如下:

努尼特的测试

```
Assert.That(actual, Is.EqualTo(expected)); 
```

变成了 xUnit 中的测试:

```
Assert.Equal(expected, actual); 
```

手动转换需要很大的耐心和毅力。因为我两者都没有，所以在手动做了几十次之后，我决定让整个事情自动化。第一种最显而易见的方法是使用一个正则表达式，将两者相互转换，如下所示:

```
/Assert\.That\((.*?), Is\.EqualTo\((.*)\)\)/ -> Assert.Equal($2, $1) 
```

它确实对一些简单的情况有效，但是加入一些更复杂的东西，整个事情就偏离了方向。一个完全有效的小测试将这个正则表达式撕成碎片:

```
Assert.That("));", Is.EqualTo("));")); -> Assert.Equal(", "));");;")); 
```

不太好。

更好的方法是将源文件解析成 AST(抽象语法树),并在其上执行源到源的转换。我过去用 C++使用 [Clang/LLVM](https://clang.llvm.org/) 和用 JavaScript 使用 [Acorn 解析器](https://github.com/acornjs/acorn)做过一些这样的事情。对于 C#有[罗斯林](https://github.com/dotnet/roslyn)。

这能有多难？让我们找出答案。有一些文档和一些样本。此外，可以使用 VS2017 生成一个启动项目，该项目将执行文件加载和最小限度的 AST 遍历。这是一个好的开始，我们可以在此基础上再接再厉。例如，这里有一个很好的[源转换的起点](https://github.com/dotnet/roslyn/wiki/Getting-Started-C%23-Syntax-Transformation)。

这是一个最简单的 NUnit 模块:

```
using NUnit.Framework;

namespace Test
{
    [TestFixture]
    class DumpTests
    {
        [Test]
        public void One_plus_one_should_be_two()
        {
            Assert.That(1 + 1, Is.EqualTo(2));
        }
    }
} 
```

当转换为 xUnit 时，它变成这样:

```
using Xunit;

namespace Test
{
    class DumpTests
    {
        [Fact]
        public void One_plus_one_should_be_two()
        {
            Assert.Equal(2, 1 + 1);
        }
    }
} 
```

准确地说，需要做以下工作:

*   更改`using`指令
*   删除`TextFixture`类属性
*   用`Fact`替换`Test`属性
*   将`Assert.That`更改为`Assert.Equal`并交换参数

语法重写器完成了所有的工作，我们只需要填充一些逻辑:

```
public class NunitToXunitRewriter: CSharpSyntaxRewriter
{
    ...
} 
```

让我们从最简单的开始，删除`TextFixture`属性:

```
public class NunitToXunitRewriter: CSharpSyntaxRewriter
{
    public override SyntaxNode VisitAttributeList(AttributeListSyntax node)
    {
        if (ShouldRemoveTestFixture(node))
            return null;

        return base.VisitAttributeList(node);
    }

    // Checks if the node is "[TestFixture]" and should be removed
    private bool ShouldRemoveTestFixture(AttributeListSyntax node)
    {
        return node.Attributes.Count == 1
            && node.Attributes[0].Name.ToString() == "TestFixture"
            && node.Parent is ClassDeclarationSyntax;
    }
} 
```

在这种情况下，代码非常简单。源文件中的每个属性都会调用`VisitAttributeList`函数。我们只是检查属性列表只有一个属性，它的名字是`TextFixture`并且父节点是一个类。如果都为真，那么我们只需从 visitor 方法中返回`null`来指示应该从语法树中删除该节点。简单。

接下来是`Test`属性:

```
public class NunitToXunitRewriter: CSharpSyntaxRewriter
{
    public override SyntaxNode VisitAttributeList(AttributeListSyntax node)
    {
        var newNode = TryConvertTestAttribute(node);
        if (newNode != null)
            return newNode;

        return base.VisitAttributeList(node);
    }

    // Converts "[Test]" to "[Fact]"
    private SyntaxNode TryConvertTestAttribute(AttributeListSyntax node)
    {
        if (node.Attributes.Count != 1)
            return null;

        if (node.Attributes[0].Name.ToString() != "Test")
            return null;

        if (!(node.Parent is MethodDeclarationSyntax))
            return null;

        return
            AttributeList(
                AttributeList<AttributeSyntax>(
                    Attribute(
                        IdentifierName("Fact"))))
            .NormalizeWhitespace()
            .WithTriviaFrom(node);
    }
} 
```

我们在这里所做的与前面的例子非常相似，除了我们没有删除节点，而是用其他东西替换它。首先，我们检查它是一个名为`Test`的单个属性，并且附加到一个函数上。为了替换它，我们需要构造一个新的语法节点。在这种情况下，都是一样的东西，只是名称不同而已。为了构建语法节点，我们使用了`SyntaxFactory`方法，比如`AttributeList`、`Attribute`等等。小怪癖是`NormalizeWhitespace`和
T5 位。这些确保了结果代码是格式化的，并且有从原始节点复制的空白。否则，输出代码会看起来不合适，需要重新格式化。

指令的改变也是微不足道的。和上面的`Fact`属性情况很像:

```
public class NunitToXunitRewriter: CSharpSyntaxRewriter
{
    public override SyntaxNode VisitUsingDirective(UsingDirectiveSyntax node)
    {
        var newNode = TryConvertUsingNunit(node);
        if (newNode != null)
            return newNode;

        return base.VisitUsingDirective(node);
    }

    // Converts "using NUnit.Framework" to "using Xunit"
    private SyntaxNode TryConvertUsingNunit(UsingDirectiveSyntax node)
    {
        if (node.Name.ToString() != "NUnit.Framework")
            return null;

        return
            UsingDirective(IdentifierName("Xunit"))
            .NormalizeWhitespace()
            .WithTriviaFrom(node);
    }
} 
```

转换是一个更复杂的情况。我们要匹配的表达式的问题是相当复杂的，尽管看起来不是那样。有一个成员函数访问`Assert.That`和一个函数调用`Assert.That(...)`，参数列表由两个参数组成，其中第二个也是成员函数调用:`Assert.That(actual, Is.EqualTo(expected))`。使用 [Roslyn Quoter](http://roslynquoter.azurewebsites.net/) 工具，可以生成创建这样一个表达式的代码:

```
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
                    IdentifierName("actual")),
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
                                    IdentifierName("expected"))))))}))) 
```

在 AST 表单中，这段代码看起来非常大。当我们想用一段不同的代码替换这个模式时，我们需要首先找到它。这意味着我们需要检查文件中每个函数调用表达式的结构，看看是否相似:

```
public class NunitToXunitRewriter: CSharpSyntaxRewriter
{
    public override SyntaxNode VisitInvocationExpression(InvocationExpressionSyntax node)
    {
        var newNode = TryConvertAssertThatIsEqualTo(node);
        if (newNode != null)
            return newNode;

        return base.VisitInvocationExpression(node);
    }

    // Converts Assert.That(actual, Is.EqualTo(expected)) to Assert.Equal(expected, actual)
    private SyntaxNode TryConvertAssertThatIsEqualTo(InvocationExpressionSyntax node)
    {
        // Check it's Assert.That member
        if (!IsMethodCall(node, "Assert", "That"))
            return null;

        // It must have exactly two arguments
        var assertThatArgs = GetCallArguments(node);
        if (assertThatArgs.Length != 2)
            return null;

        // The second argument must be a `Is.EqualTo`
        var isEqualTo = assertThatArgs[1].Expression;
        if (!IsMethodCall(isEqualTo, "Is", "EqualTo"))
            return null;

        // With exactly one argument
        var isEqualToArgs = GetCallArguments(isEqualTo);
        if (isEqualToArgs.Length != 1)
            return null;

        // Grab the arguments
        var expected = isEqualToArgs[0];
        var actual = assertThatArgs[0];

        // Build a new AST with the actual and expected nodes inserted into it
        return
            InvocationExpression(
                MemberAccessExpression(
                    SyntaxKind.SimpleMemberAccessExpression,
                    IdentifierName("Assert"),
                    IdentifierName("Equal")))
            .WithArgumentList(
                ArgumentList(
                    SeparatedList<ArgumentSyntax>(
                        new SyntaxNodeOrToken[] {expected, Token(SyntaxKind.CommaToken), actual})))
            .NormalizeWhitespace()
            .WithTriviaFrom(node);
    }
} 
```

为了匹配表达式，我们必须深入 AST 并逐节点进行比较。这非常繁琐，但幸运的是，在代码编写完成后，它将转换所有具有相似结构的测试。写一次，跑多次。此匹配代码中使用的两个辅助函数如下所示:

```
private bool IsMethodCall(ExpressionSyntax node, string objekt, string method)
{
    var invocation = node as InvocationExpressionSyntax;
    if (invocation == null)
        return false;

    var memberAccess = invocation.Expression as MemberAccessExpressionSyntax;
    if (memberAccess == null)
        return false;

    if ((memberAccess.Expression as IdentifierNameSyntax)?.Identifier.ValueText != objekt)
        return false;

    if (memberAccess.Name.Identifier.ValueText != method)
        return false;

    return true;
}

private ArgumentSyntax[] GetCallArguments(ExpressionSyntax node)
{
    return ((InvocationExpressionSyntax)node).ArgumentList.Arguments.ToArray();
} 
```

在表达式匹配的情况下，我们采用`expected`和`actual`参数，或者确切地说是代表它们的 AST 节点，并将它们封装到一个不同的 AST 中，该 AST 代表 xUnit 的等价参数:`Assert.Equal(expected, actual)`。

没那么难。但是现在我们有了一个工具，可以自动地将大多数测试从 NUnit 转换到 xUnit。它不仅转换了`Assert`表达式，还转换了整个文件。不错！

糟糕的是，匹配代码对于我们要转换的表达式来说是非常特殊的。因此，如果我们有一些`Assert`的变体，那么就需要为每种情况编写这么多代码。它会很快失去控制。想象一些非常简单的变化:

```
Assert.That(actual, Is.True());
Assert.That(actual, Is.EqualTo(true));
Assert.That(actual, Is.False());
Assert.That(actual, Is.EqualTo(false)); 
```

为了涵盖最常见的 NUnit 情况，我们必须用非常重复的代码编写数百个匹配的函数。那将是大量的工作。我们能做得更好吗？是的，我们可以！我有一个想法，我会在下一篇文章中描述。

## 结论

仅用 [175 行代码](https://gist.github.com/detunized/8d548bb3b6808f7f076ed1a5f2c6ddd4)我们就有了一个全功能的转换器，它可以在几秒钟内完成手工需要很多时间才能完成的工作。尽管这只是一个概念证明，并没有涵盖任何大量的 NUnit 断言，但我几乎不用额外的修复就可以通过测试转换一些文件。

*原载于[detunized.net](https://detunized.net/posts/2019-03-12-nunit-to-xunit-automatic-test-conversion/)T3】*