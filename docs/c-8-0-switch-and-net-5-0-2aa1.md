# C# 8.0 开关和。NET 5.0

> 原文：<https://dev.to/jeikabu/c-8-0-switch-and-net-5-0-2aa1>

随着 [Visual Studio 2019 发布](https://devblogs.microsoft.com/visualstudio/visual-studio-2019-code-faster-work-smarter-create-the-future/)，我们开始倒计时[。网芯 3.0 和 C# 8.0](//./net-core-30-preview-w-c-80-nullable-reference-types-5e53) 今秋。微软刚刚宣布了未来的计划:[。NET 5](https://devblogs.microsoft.com/dotnet/introducing-net-5/) (除其他外，还包括 [VentureBeat](https://venturebeat.com/2019/05/06/microsoft-visual-studio-online-net-5-ml-net-1-0/) 上的报道)。

## [T1。网络 5](#net-5)

除非你长期热衷于。NET 的整个生态系统”。NET Framework“/”。NET Core"/"Mono "，各种语言(c#/F #/等。)，以及与之相关的[TLA](https://en.wikipedia.org/wiki/Three-letter_acronym)——汤库微妙而令人困惑。将事物引向同一个方向并统一各种各样的技术是 2020 年秋季的目标:

*   统一。NET 框架，。NET 核心和 Xamarin/Mono 集成到单个平台中。
    *   。NET 框架和。NET Core 成为单一开源框架/运行时:“。净 5”。
    *   Mono 和 CoreCLR(。NET 核心运行时)作为替代的“运行时体验”。
*   所有平台上的 Java 互操作性，某些平台上的 Objective-C 和 Swift。
*   提高 [AOT 编译](https://en.wikipedia.org/wiki/Ahead-of-time_compilation)故事。
    *   iOS、 [WASM/WebAssembly](https://webassembly.org/) 和其他一些平台需要完整的 AOT。
    *   部分 AOT(减少启动时间和内存使用)结合 [JIT](https://en.wikipedia.org/wiki/Just-in-time_compilation) (最大化代码对泛型、反射等的支持。)有用。
    *   [Mono 有段时间有了 AOT](https://www.mono-project.com/docs/advanced/aot/) ，UWP app 有了[。网原生](https://docs.microsoft.com/en-us/dotnet/framework/net-native/)，。NET 框架有更老的 [NGEN 工具](https://docs.microsoft.com/en-us/dotnet/framework/tools/ngen-exe-native-image-generator)。

## C# 8.0 `switch`

我们[(最)对“可空引用类型”](//./net-core-30-preview-w-c-80-nullable-reference-types-5e53)(在编译时捕捉一些`null`相关的运行时异常)感到兴奋，但是 C# 8 中还有其他好东西。卑微的`switch`在 C# 7 中升级了[模式匹配](//./thrift-012-1mi9#unions)，并在 C# 8 中获得了一些新技能。

这里是来自[的起始结构。小麦的博客文章](https://devblogs.microsoft.com/dotnet/do-more-with-patterns-in-c-8-0/) :

```
class Point
{
    public int X { get; }
    public int Y { get; }
    public Point(int x, int y) => (X, Y) = (x, y);
    public void Deconstruct(out int x, out int y) => (x, y) = (X, Y);
} 
```

Enter fullscreen mode Exit fullscreen mode

**开关表达式**允许`switch`“返回”一个值:

```
// Before (switch as a statement)
string Display(object o)
{
    switch (o)
    {
        case Point p:
            return $"({p.X}, {p.Y})";
        default:
            return "unknown";
    }
}
// After (switch as an expression)
string Display(object o)
{
    return o switch
    {
        Point p => $"({p.X}, {p.Y})",
        _ => "unknown"
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

如果到达了 switch 表达式的末尾而没有进行匹配，则会引发异常。

**属性模式**允许您根据可访问的字段进一步细化匹配:

```
// Before (switch as a statement)
string Display(object o)
{
    switch (o)
    {
        case Point p when p.X == 0 && p.Y == 0:
            return "origin";
        //...
    }
}
// After (switch as an expression)
string Display(object o)
{
    return o switch
    {
        Point { X: 0, Y: 0 } /* p */ => "origin",
        Point { X: var x, Y: var y } /* p */ => $"({x}, {y})",
        {} => o.ToString(), // `o` is non-null and not a `Point`
        null => "null"
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

**位置模式**处理具有`Deconstruct()`方法的元组和类型(就像上面的`Point`，也参见[解构器](https://docs.microsoft.com/en-us/dotnet/csharp/deconstruct) ):

```
string Display(object o)
{
    return o switch
    {
        Point(0, 0) => "origin",
        Point(var x, var y) => $"({x}, {y})",
        //...
    }
}

// General case for tuples
string DisplayXY(int x, int y, bool isValid)
    (x, y, isValid) switch {
        (_, _, false) => "invalid",
        (0, 0, true) => "origin",
        //...
    } 
```

Enter fullscreen mode Exit fullscreen mode

总的来说，我对`XYZ switch`而不是`switch XYZ`的语法并不感到兴奋，但这些都是受欢迎的补充(尤其是来自具有类似特性的其他语言)。

关于更长的报道，请参见最近的 MSDN 杂志文章。