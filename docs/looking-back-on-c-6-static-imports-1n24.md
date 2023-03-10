# 回顾 C# 6:静态导入

> 原文：<https://dev.to/csmacnz/looking-back-on-c-6-static-imports-1n24>

[![Looking back on C# 6: Static Imports](img/767918e364a1f1d2e74b03172385ef0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VC6frUv8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1502426899022-660e3522a15a%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjE2ODI3fQ)

随着 C# 8 的到来，我想介绍一些我一直在使用的 C# 6 和 c# 7 语言的特性，这些特性你可能已经错过了。

另一个我曾一度回避的功能，但现在已经开始越来越多地使用了。`using`将名称空间引入文件的范围。这允许我们写`Console.WriteLine("Hi");`而不是`System.Console.WriteLine("Hi");`。但是有了这个语言特性，我们在作用域中引入了一个静态类。所以我们可以写`WriteLine("Hi");`。这一开始看起来有点奇怪，因为代码看起来与调用当前类(或基类)中的方法相同。

实现这一点的语法相当简单。

```
// This is a standard 'using'
using System;

// To pull in for the example above you would use the new syntax:
using static System.Console;

// But you can import any static class you like
using static MySystem.Helpers.LookupTables; 
```

如果谨慎使用，这可以减少代码中到处使用的强领域概念的噪音，提高可读性并节省击键次数。一直用的例子是`System.Math`。但是使用这个最适合你工作的领域。

当一种语言特性达到足够普遍的使用状态时，它的用法就变得规范了。对我来说，这个语言特性已经到了那个地步。期待在未来的代码中看到更多这种战略性的应用。