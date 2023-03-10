# 使用 TypeScript 进行彻底的类型检查！

> 原文：<https://dev.to/babak/exhaustive-type-checking-with-typescript-4l3f>

# 简介

如果你能在写下一个 bug 的时候就发现它，那不是很好吗？这就是静态类型分析的全部内容。在*编译时*捕捉你输入的 bug。这就是为什么 TypeScript 是如此重要的生产力助推器；尤其是在最严格的环境下。

TypeScript 被设计成 JavaScript 的超集，这就是为什么某些特性必须通过模式和原则来解锁。一个很好的特性就是穷举检查。

简而言之，彻底的类型检查就是当你的静态编译器检查你没有留下未检查的可能性。思考一下[空检查](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html)是如何工作的会有所帮助。

```
function test(x: string | null) {
    if (x === null) {
        return;
    }
    x; // type of x is string in remainder of function
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的函数`test`接受一个参数`x`，该参数可以是`string`或`null`。注意，在带有 return 语句的`if`块下面，TypeScript 知道`x`现在只能是`string`。

如果我们现在为`x`是`string`类型的情况添加另一个`if`块:

```
function test(x: string | null) {
  if (x === null) {
    return
  }
  if (typeof x === 'string') {
    return;
  }
  x; // type of x is never the remainder of the function
} 
```

Enter fullscreen mode Exit fullscreen mode

那么该块下面的`x`是类型`never`。`x`的所有可能性都已经用尽。我们现在准备讨论穷举检查。

# 穷举式检查

TypeScript 支持文本值作为类型。假设我们有一个函数`makeDessert`，它获取一个类型为`Fruit`的水果，并返回一个字符串值来表示制作的甜点的名称。

```
type Fruit = 'banana' | 'orange'
function makeDessert( fruit: Fruit ) {
  // make dessert from type Fruit
} 
```

Enter fullscreen mode Exit fullscreen mode

我们如何让编译器确保我们已经覆盖了`makeDessert`函数中`fruit`的所有可能值？

我们从检查所有可能的值开始:

```
type Fruit = 'banana' | 'orange'
function makeDessert( fruit: Fruit ) {
  switch( fruit ) {
    case 'banana': return 'Banana Shake'
    case 'orange': return 'Orange Juice'
  }
  x // x has type never here
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们已经检查了`banana`和`orange`，`x`在这个 switch 语句的底部有一个类型`never`。现在，我们希望*对此进行保险。*

从这里开始，有两种方法可以做到这一点。首先是指定函数的返回类型

```
function makeDessert( fruit: Fruit ): string {
  switch( fruit ) {
    case 'banana': return 'Banana Shake'
    case 'orange': return 'Orange Juice'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们指定了返回类型，如果我们没有处理特定的情况，编译器会让我们知道。然而，目前，如果想在 switch 语句下面`throw`，这个好处就没有了。TypeScript 将假设`never`是可接受的返回情况。处理收入类型为`any`且与我们的案例不符的情况可能很重要。

第二种方法——使用只接受类型为`never`的参数的函数，这就是 switch 语句底部的`x`。这样，如果类型`Fruit`改变了，`x`将不再是类型`never`。这将触发一个编译器错误，就像这样，我们有编译器支持的穷举检查。

我们来看看这个:

```
type Fruit = 'banana' | 'orange'

function exhaustiveCheck( param: never ) { }

function makeDessert( fruit: Fruit ) {
  switch( fruit ) {
    case 'banana': return 'Banana Shake'
    case 'orange': return 'Orange Juice'
  }
  exhaustiveCheck( fruit ) // ✅ no error
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果`Fruit`包含一个值`mango`，我们将收到一个编译器错误，通知我们 switch 语句底部的`fruit`可以是`mango`。

```
type Fruit = 'banana' | 'orange' | 'mango'

function exhaustiveCheck( param: never ) { }

function makeDessert( fruit: Fruit ) {
  switch( fruit ) {
    case 'banana': return 'Banana Shake'
    case 'orange': return 'Orange Juice'
  }
  exhaustiveCheck( fruit ) // 🚫 ERROR! `mango` is not assignable to type `never`
} 
```

Enter fullscreen mode Exit fullscreen mode

为了修复这个错误，我们必须将`mango`作为一个可能的值
来处理

```
type Fruit = 'banana' | 'orange' | 'mango'

function exhaustiveCheck( param: never ) { }

function makeDessert( fruit: Fruit ) {
  switch( fruit ) {
    case 'banana': return 'Banana Shake'
    case 'orange': return 'Orange Juice'
    case 'mango' : return 'Mango Smoothie'
  }
  exhaustiveCheck( fruit ) // ✅ no error, all values handled.
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，为了以防万一，我们应该防止水果的值为`any`的可能性，这将关闭类型检查并允许任何值传入 makeDessert。我们通过在`exhaustiveCheck`函数中抛出一个错误来做到这一点:

```
function exhaustiveCheck( param: never ): never { 
  throw new Error( 'should not reach here' )
} 
```

Enter fullscreen mode Exit fullscreen mode

本期 StackOverflow 概述了此解决方案:

[https://stack overflow . com/questions/39419170/how-do-I-check-a-switch-block-is-exhaustive-in-typescript](https://stackoverflow.com/questions/39419170/how-do-i-check-that-a-switch-block-is-exhaustive-in-typescript)

我们能以一种不那么冗长的方式在 TypeScript 中实现穷举检查吗？更实用的方式？

是啊是啊！😅

# 助手库

我已经创建了一个助手库

[https://github.com/babakness/exhaustive-type-checking](https://github.com/babakness/exhaustive-type-checking)

以及完整的视频教程🎬

[![](img/9efc6cfcc1dee1a954afca7be38c76d9.png)](#)

为了突出一个例子，我们可以如下创建`makeDessert`:

```
const makeFruit = matchConfig<Fruit>()
const makeDessert = match({
  banana: () => 'Banana Shake'
  orange: () => 'Orange Juice'
  mango: () => 'Mango Smoothie'
}) 
```

Enter fullscreen mode Exit fullscreen mode

这种模式提供了一种更具组合性的方式来进行彻底的检查，并具有 switch 模式所具有的自动完成的优点。

# 结论

穷举检查确实有助于减少运行时错误。事实上，即使是测试驱动的开发，如果我们从来没有编写代码来处理情况，我们的代码覆盖率统计也不会反映出我们没有测试它。

享受并随时访问我的推特和我的新 T2 YouTube 频道