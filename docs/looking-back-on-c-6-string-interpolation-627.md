# 回顾 C# 6:字符串插值

> 原文：<https://dev.to/csmacnz/looking-back-on-c-6-string-interpolation-627>

[![Looking back on C# 6: String interpolation](img/767918e364a1f1d2e74b03172385ef0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VC6frUv8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1502426899022-660e3522a15a%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

随着 C# 8 的到来，我想这是一个很好的时机来反思一下这种语言最近的新增内容。有些很棒的改进你可能错过了，有些我真的很喜欢使用，有些我认为已经达到了规范使用状态，我认为这些都值得反思。

字符串插值是一个很棒的特性，我现在可以在任何地方使用它(除了日志记录——见下文)。

我们有高效的库来为我们格式化字符串，所以我们不需要一直连接。这既方便又高效。

```
var string1 = "My name is " + "Mark";
var string2 = String.Format("My name is {0}", "Mark"); 
```

上面得到的字符串是等效的，但是第二个选项有很多好处:

*   为具有多个参数的较大字符串构建更快的内部字符串
*   惰性执行意味着您可以将`template` + `args`向下传递到堆栈中，稍后再格式化(或者在必要时从不进行优化，比如调试/详细日志)
*   作为模板可能比串联更具可读性(取决于消息)。

问题是我们有所有这些指数头寸。有很多论点时，很容易要么把立场搞错，要么改变立场犯错误。

## 字符串插值

你可以把字符串插值想象成编译器自动把你的字符串变成一个`String.Format`给你。(过于简单，但却是将其概念化的有效方法。)第一个魔术是`$`角色。将它添加到字符串的开头会使它成为一个插值字符串。这赋予了花括号特殊的权力(`{}`)。当字符串中有一组大括号时，这实质上就变成了一个表达式。你可以把任何你想要的代码放在这里，它变成了一个格式参数。这提高了可读性。

```
var oldWay = String.Format($"{0} is {1} years old.", p.Name, p.Age);
var newWay = $"{p.Name} is {p.Age} years old."; 
```

*   错误更容易发现
*   对字符串及其格式执行的编译时检查
*   性能-应用于优化的`String.Format`的相同优势
*   可读——读起来像一个句子，对论点的来源有清晰的理解

你仍然可以在你的字符串中使用`{`，你只需要用另一个`{` :
来转义它

```
var name = "bob";

// This becomes: {"name":"bob"}
var json = "{{""name"":""{name}""}}"; 
```

这是一个规范的语言特性，您将会看到大多数与字符串相关的代码使用它。也就是说，如果你还不是你自己。

## 为什么“除了伐木”？

事实证明，使用`Microsoft.Extensions.Logging`和 ILogger 进行日志记录有一个非常有用的特性。语义日志记录，也称为结构化日志记录。

支持的语义/结构化日志记录取决于日志记录的实现者，但 Serilog、Application Insights、NLog 等倾向于支持这一特性，它被定义为 API 的一级部分[。](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-message-template)

与`String.Format`的主要区别是你在模板中给出了命名的参数，而不是编号的。

```
String.Format("My name is {0}", "Mark");
_logger.LogInformation("My name is {FirstName}", "Mark"); 
```

任何真正监控过他们日志的人都知道模板真的很有价值。您可以使用模板化的参数快速找到日志消息的所有实例，而不必使用内联值进行通配符搜索，因为每条消息都是唯一的雪花。大多数日志框架使用字符串中的值(在上面的例子中是`FirstName`)将参数作为命名元数据添加。

这很有效。只要你在记录时不使用新的有用的字符串插值。字符串插值的结果是将一个字符串传递给记录器，而不是传递一个模板和参数，所以此时非此即彼。

正如你所料，[人们真的希望这个](https://stackoverflow.com/questions/52200484/why-logging-doesnt-use-string-interpolation)自动从字符串插值逆向工程到语义日志，但是争论在于这是意想不到的和突破性的变化，所以你可能不会很快看到没有自定义库或 API 参与的。