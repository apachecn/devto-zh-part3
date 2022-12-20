# 一些鲜为人知的类型脚本功能

> 原文：<https://dev.to/elie222/some-lesser-known-typescript-features-2034>

在过去几年中，TypeScript 已经成为编写 JavaScript 应用程序的一种流行方式。这种语言非常强大，可以做很多事情。

下面是一些您可能不知道的、对您的开发有帮助的罕见的 TypeScript 特性的简短列表:

1.  你可以用下划线写数字。比如`1000000000`可以写成`1_000_000_000`。它将被编译成 Javascript 中的常规数字，但是使用这种语法使事情更容易阅读。下划线可以放在数字的任何地方，尽管写`25`比写`2_5`更易读。

2.  如果你知道一个变量被定义了，你可以在它后面添加一个`!`来访问它，而不需要首先检查值是否为`undefined`。例如，您可以使用装饰器(例如`@inject(router)`或`@inject(store)`)将道具注入 React 组件。你现在可以写`this.props.store!.someValue`，即使商店的定义是`store?: Store`。

注意不要过度使用这个特性，但是有时候它会派上用场。关于这个问题的进一步讨论，可以在这里阅读[。(您也可以总是使用 if 语句，但是如果您知道 if 语句是真的，那就更加冗长了)。](https://github.com/mobxjs/mobx-react/issues/256)

1.  您可以将变量设置为类型`never`。这意味着它不能被设置为任何值。这有什么用？它可能有用的一个地方是检查您是否已经处理了 switch 语句的每一种可能性。例如:

```
const someFunction = (temperature: 'hot' | 'cold' | 'warm') => {
  switch (temperature) {
    'hot': console.log("it's hot outside!"); break
    'cold': console.log("it's cold outside!"); break
    default: const x: never = temperature;
  }
} 
```

上面的代码会抛出一个编译时错误，因为可以到达`x`。如果您为 warm 添加一个 switch case，并在其后添加一个 break，编译错误将会消失，因为无法再到达默认块。

1.  您可以使用的另一个较少使用的类型是`unknown`。这只是意味着我们不知道我们有什么项目。例如，如果我们调用第三方 API，这可能会发生。如果我们尝试 do `x.substr(0, 2)`它将抛出一个编译错误。我们可以通过首先进行类型检查来使用未知变量。例如，写`if (typeof x === “string”) x.substr(0, 2)`将不再抛出一个错误。

没有。应该包括其他有用的打字技巧吗？欢迎在评论中提及它们！