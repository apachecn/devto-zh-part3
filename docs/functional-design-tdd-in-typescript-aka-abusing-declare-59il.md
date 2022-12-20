# 功能设计:TypeScript 中的 TDD(也称为滥用“声明”)

> 原文：<https://dev.to/gcanti/functional-design-tdd-in-typescript-aka-abusing-declare-59il>

驱动开发(TDD)是一种技术，用于将一个问题分解成一组更小的问题，让类型检查器建议具体的实现，或者至少帮助我们实现。这里有一个实际的例子。

比方说，我们想要重新实现函数`Promise.all`，我们将其命名为`sequence`。先说它的招牌

```
// TODO
declare function sequence<T>(promises: Array<Promise<T>>): Promise<Array<T>> 
```

注意，一个`declare` d 函数，即使还没有实现，也是立即可用的，并且可以并发地对我们代码的剩余部分进行类型检查。如果我们运行一个构建系统，我们会得到一个错误，因为在运行时没有这样的函数，它现在只存在于“类型的世界”中。

然而，这项技术的目标是**尽可能长时间地在编辑器中工作，而不需要运行我们的代码**。我们将完全依靠类型检查器来确保在我们进行的过程中它不会引发任何错误。

回到我们的问题，我们需要将类型为`A`的值的数组转换(或“缩减”)为类型为`B`的值。

我们正在寻找的转换可能是`reduce`，它实际上有下面的签名

```
declare function reduce<A, B>(this: Array<A>, f: (acc: B, x: A) => B, init: B): B 
```

我们还不知道如何实现`f`和`init`，但是我们可以像以前一样跳过具体的实现。现在，只需简单地`declare`缺失的位，并用我们正在处理的相应类型
替换类型参数`A`和`B`就足够了

```
A = Promise<T>

B = Promise<Array<T>> 
```

这就是我们得到的

```
// TODO
declare function pushPromise<T>(acc: Promise<Array<T>>, x: Promise<T>): Promise<Array<T>>

// TODO
function sequence<T>(promises: Array<Promise<T>>): Promise<Array<T>> {
  declare const init: Promise<Array<T>> // TypeScript error
  return promises.reduce(pushPromise, init)
} 
```

唉`declare`不能在函数体内使用，所以我们需要一个临时的解决方法

```
declare const TODO: any

// TODO
declare function pushPromise<T>(acc: Promise<Array<T>>, x: Promise<T>): Promise<Array<T>>

// partially implemented
function sequence<T>(promises: Array<Promise<T>>): Promise<Array<T>> {
  const init: Promise<Array<T>> = TODO
  return promises.reduce(pushPromise, init)
} 
```

现在`init`很简单，我们可以将一个空数组放入承诺“容器”

```
const init: Promise<Array<T>> = Promise.resolve([]) 
```

实现`pushPromise`更复杂，我们知道如何将一个类型为`Array<T>`的值与一个类型为`T`的值连接起来，以获得另一个类型为`Array<T>`的值，我们将其命名为`push`

```
declare function push<T>(x: Array<T>, y: T): Array<T> 
```

但是我们如何在`pushPromise`中将`acc`和`x`连接起来，因为它们都是承诺？

我们想要的是一个过程，让我们把它命名为`liftA2`，它可以“提升”函数`push`，产生一个新的函数，这个函数可以处理承诺“内部”的值。同样我们只是`declare`预期的结果而没有任何实现

```
// TODO
declare function liftA2<A, B, C>(
  f: (a: A, b: B) => C
): (fa: Promise<A>, fb: Promise<B>) => Promise<C>

// TODO
declare function push<T>(x: Array<T>, y: T): Array<T>

// implemented
function pushPromise<T>(acc: Promise<Array<T>>, x: Promise<T>): Promise<Array<T>> {
  return liftA2<Array<T>, T, Array<T>>(push)(acc, x)
}

// implemented
function sequence<T>(promises: Array<Promise<T>>): Promise<Array<T>> {
  const init: Promise<Array<T>> = Promise.resolve([])
  return promises.reduce(pushPromise, init)
} 
```

现在我们只需要实现`liftA2`和`push`

```
function liftA2<A, B, C>(
  f: (a: A, b: B) => C
): (fa: Promise<A>, fb: Promise<B>) => Promise<C> {
  return (a, b) => a.then(aa => b.then(bb => f(aa, bb)))
}

function push<T>(x: Array<T>, y: T): Array<T> {
  return x.concat([y])
}

function pushPromise<T>(acc: Promise<Array<T>>, x: Promise<T>): Promise<Array<T>> {
  return liftA2<Array<T>, T, Array<T>>(push)(acc, x)
}

function sequence<T>(promises: Array<Promise<T>>): Promise<Array<T>> {
  const init: Promise<Array<T>> = Promise.resolve([])
  return promises.reduce(pushPromise, init)
} 
```

让我们试一试

```
sequence([]).then(x => console.log(x)) // []
sequence([Promise.resolve(1), Promise.resolve(2), Promise.resolve(3)]).then(x =>
  console.log(x)
) // [1, 2, 3] 
```

正如你所看到的，强类型系统不仅可以防止错误，还可以在设计过程中为你提供指导和反馈。