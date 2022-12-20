# 使用。用于创建可发现标准的 editorconfig

> 原文：<https://dev.to/calvinallen/using-editorconfig-in-visual-studio-to-create-discoverable-standards-4eei>

> 我对今年由我的好友 Matt Groves 主持的 CSAdvent 的贡献，就是试图展示 Visual Studio 中的编码和命名标准。访问[https://crosscuttingconcerns.com/The-Second-Annual-C-Advent](https://crosscuttingconcerns.com/The-Second-Annual-C-Advent)查看完整的帖子日历！

从 Visual Studio 2017 开始，我们可以选择使用“.”。editorconfig”文件来定义大量的编辑器标准——从基本的东西开始，比如“制表符对空格”:

```
[*.{cs,vb}]
indent_style = tab //TABS RULE
indent_size = 4 
```

Enter fullscreen mode Exit fullscreen mode

对于更高级的项目，如“最好用花括号将代码块括起来”(具有相关的严重性)

```
[*.cs]
csharp_prefer_braces = true:none //"PREFER" them, but don't highlight the code in the editor 
```

Enter fullscreen mode Exit fullscreen mode

后退一点。editorconfig 文件/格式是微软在 Visual Studio 中作为一等公民采用的开放标准。开放标准没有定义具体的编码风格/标准(就像我们在上面看到的`csharp_prefer_braces`)，所以那些只适用于知道它们的插件或工具(例如，Visual Studio——我希望，最终也是 VSCode)

对我来说，最重要的是能够定义各种标准，以及定义它们的严重性，所有这些都不需要第三方工具(比如 ReSharper)。

我能够在一个纯文本文件中定义这个规则，并简单地将它签入我的存储库中。

您可以应用大量的规则，比如`csharp_prefer_braces`，来指定您喜欢的修饰符的确切顺序:

```
# CSharp and Visual Basic code style settings:
[*.{cs,vb}]
dotnet_style_require_accessibility_modifiers = always:suggestion

# CSharp code style settings:
[*.cs]
csharp_preferred_modifier_order = public,private,protected,internal,static,extern,new,virtual,abstract,sealed,override,readonly,unsafe,volatile,async:suggestion

# Visual Basic code style settings:
[*.vb]
visual_basic_preferred_modifier_order = Partial,Default,Private,Protected,Public,Friend,NotOverridable,Overridable,MustOverride,Overloads,Overrides,MustInherit,NotInheritable,Static,Shared,Shadows,ReadOnly,WriteOnly,Dim,Const,WithEvents,Widening,Narrowing,Custom,Async:suggestion 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，VB.NET 也受到支持。规则名称本身表明目标:

```
dotnet_*
csharp_*
visual_basic_* 
```

Enter fullscreen mode Exit fullscreen mode

你可能会想，“这很好，卡尔文，但这如何使他们更容易被发现？”。精彩的问题。“可发现性”在于大多数(不是全部)规则可以接受的严重性评级:

| 严重 | 描述 |
| --- | --- |
| 没有人 | 当违反规则时，不向用户显示任何内容。具有这种严重性的规则永远不会出现在快速操作或重构菜单中。 |
| 沉默/重构 | 当违反规则时，不会向用户显示任何内容，但是，这些规则会显示在快速操作或重构菜单中。 |
| 建议 | 当违反此样式规则时，在前两个字符下显示为灰点。 |
| 警告 | 当违反这个样式规则时，将它作为编译器警告显示给用户 |
| 错误 | 当违反这种样式规则时，将它作为编译器错误显示给用户 |

正如您所看到的，选择适当的严重性将有助于向开发人员展示这些规则，就在 ide 内部。

我已经开始倾向于警告严重性，因为建议风格很难看到(在我看来)。

到目前为止，我们只讨论了风格标准，但是你的好的“ole 时尚”命名标准呢？我们需要私有变量以下划线开头，没有例外！嗯，你也可以这么做！

当然，命名标准有点复杂，所以让我们看一个例子。

下面这个例子定义了所有的局部函数都是用 PascalCase 命名的:

```
[*.{cs,vb}]
dotnet_naming_rule.local_functions_should_be_pascal_case.severity = suggestion
dotnet_naming_rule.local_functions_should_be_pascal_case.symbols = local_functions
dotnet_naming_rule.local_functions_should_be_pascal_case.style = local_function_style

dotnet_naming_symbols.local_functions.applicable_kinds = local_function

dotnet_naming_style.local_function_style.capitalization = pascal_case 
```

Enter fullscreen mode Exit fullscreen mode

这个块定义了各种各样的东西，但最终说的是任何“local_function”(适用种类)都是“建议”(严重性)在“pascal_case”(大写)里。

这些类型的规则有点难以理解，所以我建议查看一下 Visual Studio 文档以获得更好的理解。

如果你有任何问题或意见，请在下面留下，我会尽我所能帮助你！