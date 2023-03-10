# 回顾 C# 6:表达式的名称

> 原文：<https://dev.to/csmacnz/looking-back-on-c-6-nameof-expressions-42cp>

[![Looking back on C# 6: nameof expressions](img/767918e364a1f1d2e74b03172385ef0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VC6frUv8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1502426899022-660e3522a15a%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

随着 C# 8 的到来，我想介绍一些我一直在使用的 C# 6 和 c# 7 语言的特性，这些特性你可能已经错过了。

这是一个很小的功能，但我在任何地方都使用它。

`nameof` expression 关键字的作用类似于一个函数，它将任何引用作为其参数，并计算表示引用名称的字符串，而不是它的计算值。这是一个编译时特性，允许您以引用的方式将代码表示为字符串。因为这是一个表达式，也是一种编译时行为，所以它可以与大多数其他运行时代码不能与代码元素交互。

查看它所解决的问题是开始了解这个特性的好地方。

```
var myVar = "This is a variable";
var message = "This message references the name of `myVaar`"; // see typo

// Using the new `nameof` expression
var message2 = String.Format("This message references the name of `{0}`" + nameof(myVar));
// if you used the type `nameof(myVaar)`, the code would not compile 
```

因为传递给`nameof`的参数是参照完整性的，所以不能有拼写错误。代码重构也将这些视为引用，因此重命名也正确地重构了这些引用。

表达式可以传递变量、方法、类、属性，几乎任何东西。你甚至可以完全限定`nameof(MyNameSpace.MyClass.MyStaticProperty)`来正确生成字符串`"MyStaticProperty"`。(注意，只生成了最后一部分。)

我使用它的一个地方是参数异常:

```
public static void DoStuff(string aValue, int aNumber)
{
    if(aValue == null)
    {
        throw new ArgumentNullException(nameof(aValue));
    }

    if(aNumber < 0)
    {
        throw new ArgumentOutOfRangeException(nameof(aNumber), "Must be Positive");
    }

    // Stuff happens here
} 
```

在 Xunit 测试中，还有一个地方我会用到它。您可以将`MemberData`与`Theory`测试结合使用，以编程方式提供数据。在字符串上使用反射来定位具有给定名称的属性或方法。在这里再次使用`nameof`避免了在重命名事物后出现拼写错误或失败的风险。

```
[Theory]
[MemberData(nameof(PositiveTestCaseData))]
public async Task WhenIDoStuff_ThenTheRightStuffHappens(string stuff, string expectedOutput)
{
    // ...
} 
```

这对于一般的日志记录来说也是很棒的，所以你可以引用特定的字段、类型和属性，而不用担心名字不同步。

一个最小但最强大的特性，如果你还没有开始在你的代码中使用的话。希望，如果你自己没有使用它，当你阅读别人的代码时，这个解释可以帮助你理解。