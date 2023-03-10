# 类型系统如何改进您的 JavaScript 代码

> 原文：<https://dev.to/iwilsonq/how-a-type-system-improves-your-javascript-code-2jpk>

普通 JavaScript 本质上是非类型化的，有些人会称之为“智能”，因为它能够判断出什么是数字或字符串。

这使得运行 JavaScript 代码更加容易，只需在浏览器或 Node.js 运行时执行即可。然而，它容易出现大量的运行时错误，破坏用户体验。

如果你曾经经历过以下情况，你会从采用类型系统中受益。

*   获取数据列表后，您可能会发现某个字段在某个记录中不存在，导致应用程序崩溃，除非显式处理。
*   您导入的类的实例没有您试图调用的方法。
*   您的 IDE 不知道哪些方法和属性是可用的，所以它不能很容易地帮助您自动完成。
*   代码推理困难，类型系统一览使重构变得更容易

## 流程、打字稿或推理

假设您有一个希望防弹的现有代码库。对于类型错误，您可以尝试采用 Flow 或 TypeScript——它们有非常相似的语法。

另一方面，在一个大型的现有代码库中采用其中之一是困难的。您将有一个塞满了与创建类型和接口相关的任务的积压工作，这些任务的代码不是以类型友好的方式设计的。

最重要的是，Flow 和 TypeScript 在它们的代码中没有提供 100%的类型安全。

推理通过推理实现了完美的类型安全，并使注释变量和函数签名变得更加简单。

## 简单而明显做作的例子

考虑下面的代码:

```
let add = (a, b) => a + b; 
```

在 basic JavaScript 中，这些参数可以是数字或字符串。在 TypeScript 或 Flow 中，这些参数可以这样注释

```
let add = (a: number, b: number) => a + b 
```

第一个代码片段知道我们正在添加两个 int 值。不是两个浮点数也不是两个字符串，这些加法运算有不同的操作符。JavaScript 很难区分 int 和 float。

现在请允许我用一个人为的例子来揭示这种渐进的打字方式。

```
let add = (a: string, b: number) => a + b
add('some string', 5) // outputs: "some string5" 
```

那个功能管用！这太荒谬了！那么理性是如何处理这个问题的呢？

```
let add = (a, b) => a + b;
add("some string", 5);
/*
This has type:
  string
but somewhere wanted:
  int
*/ 
```

这个函数在实现层面上有缺陷。Reason 对 int、float 和 string 加法有不同的操作符。

这个虚构的例子的目的是为了表明，即使不会*破坏*应用程序，仍然很有可能出现奇怪类型的“错误”。

在纯理性程序中，开发人员不必处理与类型或空值有关的产品错误。

## 开发者体验

TypeScript 最简洁的特性之一是您在代码编辑器建议中得到的增强&自动完成。

这是 TypeScript 占优势的一个方面，因为 TypeScript 程序不需要完全编译就能提供自动完成功能。理性让你在给出有用的自我暗示之前修正语法或类型上的错误。

在 VSCode 上就是这种情况，但是我知道开发人员使用 vim 的许多原因。我无法回答每位编辑的支持程度。

尽管我是 ReasonML 的忠实粉丝，但我已经用 TypeScript 或 Flow 构建了生产应用程序，我也很喜欢它们。考虑到 TypeScript 背后有大量的写作和社区支持，TypeScript 背后的浪潮使得它最近变得特别好。

另一方面，理性更难理解，因为它背后的文字和内容相对较少。通过这样的博客帖子，我希望能改变这种情况。

如果你对检查原因感兴趣，我会从这里的的[文档开始。此外，一定要在推特上关注像](https://reasonml.github.io)[@乔德沃克](https://twitter.com/@jordwalke)、 [@jaredforsyth](https://twitter.com/@jaredforsyth) 和 [@sgrove](https://twitter.com/@sgrove) 这样的人。他们对 ReasonML/OCaml 生态系统非常了解。

如果你想知道 ReasonML 如何与 GraphQL 无缝协作，可以看看我写的这篇关于 ReasonML 与 GraphQL 的文章，类型安全 Web 应用的未来。

如果你想继续关注未来的帖子，[在这里注册我的时事通讯](https://buttondown.email/iwilsonq)！