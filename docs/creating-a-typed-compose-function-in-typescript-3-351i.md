# 在 TypeScript 中创建类型化的“合成”函数

> 原文：<https://dev.to/ascorbic/creating-a-typed-compose-function-in-typescript-3-351i>

我最近写了几个小的实用函数，它们变成了对 TypeScript 泛型以及 TypeScript 3 中新的类型化元组的深入探索。这篇文章假设你至少了解一点 TypeScript 中的泛型。如果你不知道，我强烈推荐[阅读它们](https://www.typescriptlang.org/docs/handbook/generics.html)，因为它们是语言中最强大的特性之一。它假设没有函数式编程的先验知识。

函数式编程的核心概念之一是组合，即几个函数组合成一个函数来执行所有的任务。一个函数的输出被传递给另一个函数的输入。很多库都包含了一个`compose`函数来帮助解决这个问题，包括 Lodash 和 Ramda，它也是 React 中经常使用的一个模式。这些助手可以让你把这个:

```
const output = fn1(fn2(fn3(fn4(input)))); 
```

Enter fullscreen mode Exit fullscreen mode

成这样:

```
const composed = compose(fn1, fn2, fn3, fn4);

const output = composed(input); 
```

Enter fullscreen mode Exit fullscreen mode

新的`composed`函数可以根据需要重用。`compose`的搭档是`pipe`，只是把论点反过来组合。在这种情况下，参数是按照执行的顺序，而不是嵌套时出现的顺序。在上面的合成示例中，首先调用的是内部的`fn4`函数，其输出传递给`fn3`，然后传递给`fn2`和`fn1`。在很多情况下，更直观的做法是将函数想象成一个 Unix 管道，将值传到左边，然后通过管道从一个函数传到下一个函数。作为一个虚构的例子，查找目录中最大文件的 shell 命令应该是:

```
du -s * | sort -n | tail 
```

Enter fullscreen mode Exit fullscreen mode

想象一下，在一个假想的 JavaScript 环境中:

```
const largest = tail(sort(du("*"))); 
```

Enter fullscreen mode Exit fullscreen mode

您可以用管道来实现它，如下所示:

```
const findLargest = pipe(du, sort, tail);
const largest = findLargest("*"); 
```

Enter fullscreen mode Exit fullscreen mode

在一个更现实的例子中，想象加载一个 JSON 文件，对它执行几个操作，然后保存它。

最好的是，一个真实世界的例子:我实现了这个，因为我正在研究一个 Alexa 测验技巧，在那里我使用了一种函数式方法来处理请求。我通过一系列处理程序传递一个包含请求和响应数据的对象，这些处理程序检查用户是否回答了问题，然后询问下一个问题，如果合适的话，完成游戏。

```
const handler = pipe(handleAnswer, askQuestion, completeGame);
const params = handler(initialParams); 
```

Enter fullscreen mode Exit fullscreen mode

为此，我已经为 params 定义了接口，并希望能够键入参数。我希望`handler`的签名与传入函数的签名相匹配。我还希望 TypeScript 确保传入的所有函数都是同一类型的。我还希望它能够推断出这一点，因为不必要地指定类型很烦人。我找不到任何支持任意数量参数的 TypeScript 库，所以让我们继续写一个。

首先，让我们编写实际的函数，然后解决如何对它们进行类型化。这个用内置的 [`Array.reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 相当简单。我们将从`pipe`开始，因为它不需要改变参数顺序。让我们提醒自己`reduce`最简单形式的签名:

```
function reduce(callbackfn: (previousValue: T, currentValue: T) => T): T; 
```

Enter fullscreen mode Exit fullscreen mode

为数组中的每个元素传递一个回调函数。传递给回调的第一个参数是上一个回调的返回值。第二个参数是数组中的下一个值。对于 reduce 的简短版本，第一次回调调用实际上将数组中的第一个元素作为`previousValue`传递，第二个元素作为`currentValue`传递。还有一个更长的版本，它允许您传递一个初始值，如果返回值与数组元素的类型不同，您就需要这样做。我们将从更简单的版本开始:

```
export const pipe = (...fns) =>
  fns.reduce((prevFn, nextFn) => value => nextFn(prevFn(value))); 
```

Enter fullscreen mode Exit fullscreen mode

这一步一步地逐步构建组合函数，当我们遍历它时，在数组中添加下一个函数。回调函数返回一个新函数，这个新函数又调用`prevFn`(这个函数是由数组中前面的函数组合而成的)，然后将其封装在对`nextFn`的调用中。在每次调用时，它在下一次调用中包装函数，直到最后我们有一个函数调用数组中的所有元素。

```
export const pipe = <R>(...fns: Array<(a: R) => R>) =>
  fns.reduce((prevFn, nextFn) => value => nextFn(prevFn(value))); 
```

Enter fullscreen mode Exit fullscreen mode

这看起来很令人困惑(正如泛型类型经常做的那样)，但并不像看起来那么糟糕。`<R>`是函数返回类型的占位符。虽然这是一个泛型函数，但巧妙的是，TypeScript 可以从传递给它的参数类型中推断出这种类型:如果你向它传递一个字符串，它知道它将返回一个字符串。签名表明`pipe`接受任意数量的参数，这些函数只接受一个参数，并返回与该参数相同类型的值。然而这并不完全正确:`pipe`至少需要一个论证。我们需要更改签名以显示第一个参数是必需的。我们通过添加一个相同类型的初始参数，然后将其作为第二个参数传递给`reduce`，这意味着它被用作初始值。

```
export const pipe = <R>(fn1: (a: R) => R, ...fns: Array<(a: R) => R>) =>
  fns.reduce((prevFn, nextFn) => value => nextFn(prevFn(value)), fn1); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经定义了`pipe`，定义`compose`就像把`nextFn`和`prevFn` :
的顺序对调一样简单

```
export const compose = <R>(fn1: (a: R) => R, ...fns: Array<(a: R) => R>) =>
  fns.reduce((prevFn, nextFn) => value => prevFn(nextFn(value)), fn1); 
```

Enter fullscreen mode Exit fullscreen mode

在我们继续之前，我们需要测试它是否如预期的那样工作。我喜欢使用 [Jest](https://jestjs.io/) 进行测试，所以让我们定义一些测试来看看它应该如何工作:

```
import { compose, pipe } from "./utils";

describe("Functional utils", () => {
  it("composes functions", () => {
    const fn1 = (val: string) => `fn1(${val})`;
    const fn2 = (val: string) => `fn2(${val})`;
    const fn3 = (val: string) => `fn3(${val})`;
    const composedFunction = compose(fn1, fn2, fn3);
    expect(composedFunction("inner")).toBe("fn1(fn2(fn3(inner)))");
  });
  it("pipes functions", () => {
    const fn1 = (val: string) => `fn1(${val})`;
    const fn2 = (val: string) => `fn2(${val})`;
    const fn3 = (val: string) => `fn3(${val})`;

    const pipedFunction = pipe(fn1, fn2, fn3);
    expect(pipedFunction("inner")).toBe("fn3(fn2(fn1(inner)))");
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这些函数只是返回显示它们被调用的字符串。如果你不知道反勾号语法的话，他们使用的是模板文字。看看组合函数的类型，看看它们是如何工作的，并尝试改变函数的签名，看看类型检查是否有效。

[![inferred function signature](img/2ffe06b52c2d232b4f7c9265aeccd6a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i0za7xhQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qcfqsxrw8lmzu1laxwrp.png)

这里可以看到`pipedFunction`的类型是从传递给`pipe`的函数类型中推断出来的。

[![bad function type](img/9bb0f8ec032127a4504a1f055bf77523.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--knX4FXB7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jdtoy69r64f5kx7fvqoy.png)

这里我们可以看到，将`fn2`改为期望一个数字会导致类型错误:函数应该都有相同的签名。

如果我们传递一个接受多个参数的函数，我们也会得到类似的错误。然而，这不一定是对`compose`或`pipe`函数的限制。严格地说，第一个函数可以接受任何东西，只要它返回相同的类型，所有其他函数都接受一个参数。我们应该能够得到以下工作:

```
it("pipes functions with different initial type", () => {
  const fn1 = (val: string, num: number) => `fn1(${val}-${num})`;
  const fn2 = (val: string) => `fn2(${val})`;
  const fn3 = (val: string) => `fn3(${val})`;

  const pipedFunction = pipe(fn1, fn2, fn3);
  expect(pipedFunction("inner", 2)).toBe("fn3(fn2(fn1(inner-2)))");
}); 
```

Enter fullscreen mode Exit fullscreen mode

组合函数或管道函数应该与第一个函数具有相同的签名。那么这个应该怎么打呢？我们可以改变`fn1`的类型来允许不同的参数，但是这样我们就失去了这些参数的类型安全性:

```
export const pipe = <R>(
  fn1: (...args: any[]) => R,
  ...fns: Array<(a: R) => R>
) => fns.reduce((prevFn, nextFn) => value => nextFn(prevFn(value)), fn1); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要另一个泛型类型来表示`fn1`的参数。在 TypeScript 3 之前，我们不得不添加重载:

```
export function pipe<T1, R>(
  fn1: (arg1: T1) => R,
  ...fns: Array<(a: R) => R>
): (arg1: T1) => R;

export function pipe<T1, T2, R>(
  fn1: (arg1: T1, arg2: T2) => R,
  ...fns: Array<(a: R) => R>
): (arg1: T1, arg2: T2) => R;

export function pipe<T1, T2, T3, R>(
  fn1: (arg1: T1, arg2: T2, arg3: T3) => R,
  ...fns: Array<(a: R) => R>
): (arg1: T1, arg2: T2, arg3: T3) => R;

export function pipe<T1, T2, T3, T4, R>(
  fn1: (arg1: T1, arg2: T2, arg3: T3, arg4: T4) => R,
  ...fns: Array<(a: R) => R>
): (arg1: T1, arg2: T2, arg3: T3, arg4: T4) => R;

export function pipe<R>(
  fn1: (...args: any[]) => R,
  ...fns: Array<(a: R) => R>
): (a: R) => R {
  return fns.reduce((prevFn, nextFn) => value => nextFn(prevFn(value)), fn1);
} 
```

Enter fullscreen mode Exit fullscreen mode

这显然是荒谬的。幸运的是 TypeScript 3 引入了[类型化的`...rest`参数](https://blogs.msdn.microsoft.com/typescript/2018/07/30/announcing-typescript-3-0/#tuples-and-parameters)。这让我们可以这样做，它适用于任意数量的参数:

```
export const pipe = <T extends any[], R>(
  fn1: (...args: T) => R,
  ...fns: Array<(a: R) => R>
) => {
  const piped = fns.reduce(
    (prevFn, nextFn) => (value: R) => nextFn(prevFn(value)),
    value => value
  );
  return (...args: T) => piped(fn1(...args));
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了一个新的泛型类型`T`，它表示第一个函数的参数。在 TypeScript 3 之前，这段代码给出了一个错误，但是现在通过声明它`extends any[]`，编译器将其作为类型化元组参数列表接受。我们不能通过`fn1`直接减少，就像我们以前做的那样，因为它现在是一种不同的类型。相反，我们将标识函数`value => value`作为第二个值传递——这个函数只返回它的参数，不变。然后，我们将简化的函数包装在另一个具有正确类型的函数中，并将其返回。

这为我们提供了一个管道函数，其类型与第一个参数相同:

[![inferred type](img/286cf288767fb0186e8df414e91d417f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3s18SGdx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l2hqqdsysr3wr5siibj1.png)

它还对其他参数进行类型检查:它们必须都是接受第一个函数返回的相同类型的一个参数的函数，并且它们必须都返回相同的类型。

那么这给`compose`留下了什么？不幸的是，我们不能以同样的方式键入它。当`pipe`从传递给它的第一个函数中获取类型时，`compose`使用最后一个函数的类型。在参数列表的开头输入需要`...rest`个参数，现在还不支持:

# [![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) 固定参数最后出现在变量自变量函数 #1360](https://github.com/microsoft/TypeScript/issues/1360) 

[![JeroMiya avatar](img/68f4b55eb65a947d158ebcd179983bae.png)](https://github.com/JeroMiya) **[JeroMiya](https://github.com/JeroMiya)** posted on [<time datetime="2014-12-03T22:18:58Z">Dec 03, 2014</time>](https://github.com/microsoft/TypeScript/issues/1360)

摘录本期建议:[https://github.com/Microsoft/TypeScript/issues/1336](https://github.com/Microsoft/TypeScript/issues/1336)

目前，变量参数列表仅支持变量参数作为函数的最后一个参数:

```
function foo(arg1: number, ...arg2: string[]) {
}
```

Enter fullscreen mode Exit fullscreen mode

这会编译成以下 javascript:

```
function foo(arg1) {
    var arg2 = [];
    for (var _i = 1; _i < arguments.length; _i++) {
        arg2[_i - 1] = arguments[_i];
    }
}
```

Enter fullscreen mode Exit fullscreen mode

但是，可变参数函数仅限于作为最后一个参数出现，而不是第一个参数。我建议支持先出现一个可变参数，然后出现一个或多个固定参数:

```
function subscribe(...events: string[], callback: (message: string) => void) {
}

// the following would compile
subscribe(message => alert(message)); // gets all messages
subscribe('errorMessages', message => alert(message));
subscribe(
  'errorMessages',
  'customMessageTypeFoo123', 
  (message: string) => {
     alert(message);
  });

// the following would not compile
subscribe(); // supplied parameters do not match any signature of call target
subscribe('a1'); // argument of type 'string' does not match parameter of type '(message: string) => void'
subscribe('a1', 'a2'); // argument of type 'string' does not match parameter of type '(message: string) => void'
```

Enter fullscreen mode Exit fullscreen mode

subscribe 编译以下 JavaScript:

```
function subscribe() {
  var events= [];
  var callback = arguments[arguments.length - 1];
  for(var _i = 0; _i < arguments.length - 2; _i++) {
    events[_i] = arguments[_i];
  }
}
```

Enter fullscreen mode Exit fullscreen mode

注意:类型检查时，typescript 代码应该不可能调用没有参数的函数。如果 JS 或无类型 TS 代码不带参数调用它，回调将是未定义的。然而，函数开头的固定参数也是如此。

编辑:对于固定最后/可变参数优先函数，使用了一个更加现实/激励人心的例子。

[View on GitHub](https://github.com/microsoft/TypeScript/issues/1360)

在那之前，你需要坚持编写只接受一个参数的函数。

最终库在这里:

```
export const pipe = <T extends any[], R>(
  fn1: (...args: T) => R,
  ...fns: Array<(a: R) => R>
) => {
  const piped = fns.reduce(
    (prevFn, nextFn) => (value: R) => nextFn(prevFn(value)),
    value => value
  );
  return (...args: T) => piped(fn1(...args));
};

export const compose = <R>(fn1: (a: R) => R, ...fns: Array<(a: R) => R>) =>
  fns.reduce((prevFn, nextFn) => value => prevFn(nextFn(value)), fn1); 
```

Enter fullscreen mode Exit fullscreen mode

可以显示用法的测试:

```
import { compose, pipe } from "./utils";

describe("Functional helpers", () => {
  it("composes functions", () => {
    const fn1 = (val: string) => `fn1(${val})`;
    const fn2 = (val: string) => `fn2(${val})`;
    const fn3 = (val: string) => `fn3(${val})`;
    const composedFunction = compose(fn1, fn2, fn3);
    expect(composedFunction("inner")).toBe("fn1(fn2(fn3(inner)))");
  });

  it("pipes functions", () => {
    const fn1 = (val: string) => `fn1(${val})`;
    const fn2 = (val: string) => `fn2(${val})`;
    const fn3 = (val: string) => `fn3(${val})`;

    const pipedFunction = pipe(fn1, fn2, fn3);

    expect(pipedFunction("inner")).toBe("fn3(fn2(fn1(inner)))");
  });

  it("pipes functions with different initial type", () => {
    const fn1 = (val: string, num: number) => `fn1(${val}-${num})`;
    const fn2 = (val: string) => `fn2(${val})`;
    const fn3 = (val: string) => `fn3(${val})`;
    const pipedFunction = pipe(fn1, fn2, fn3);

    expect(pipedFunction("inner", 2)).toBe("fn3(fn2(fn1(inner-2)))");
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode