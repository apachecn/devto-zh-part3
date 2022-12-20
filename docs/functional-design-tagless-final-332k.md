# 功能设计:无标签最终版

> 原文：<https://dev.to/gcanti/functional-design-tagless-final-332k>

在[的上一篇文章](https://dev.to/gcanti/functional-design-how-to-make-the-time-combinator-more-general-3fge)中，我写了一个`time`组合子，它模拟了类似的 Unix 命令:给定一个动作`IO<A>`，我们可以派生出一个动作`IO<[A, number]>`，它返回运行时间和计算值

```
import { IO, io } from 'fp-ts/IO'
import { now } from 'fp-ts/Date'

export function time<A>(ma: IO<A>): IO<[A, number]> {
  return io.chain(now, start => io.chain(ma, a => io.map(now, end => [a, end - start])))
} 
```

Enter fullscreen mode Exit fullscreen mode

但是仍然有一个问题:`time`只能和`IO`一起工作。

如果我们想要一个`Task`的`time`组合子呢？还是`TaskEither`？甚至`ReaderTaskEither`？

## 一个小小的改写

让我们把`io`重新命名为一个通用的`M`(比如 **M** onad)

```
import { IO, io as M } from 'fp-ts/IO'
import { now } from 'fp-ts/Date'

export function time<A>(ma: IO<A>): IO<[A, number]> {
  return M.chain(now, start => M.chain(ma, a => M.map(now, end => [a, end - start])))
} 
```

Enter fullscreen mode Exit fullscreen mode

由`fp-ts/IO`模块导出的值`io`，包含`IO`的`Monad`实例。

在`fp-ts`中，类型类被编码为`interfaces`,实例被编码为静态字典，包含由类型类定义的操作。

因此，在一个`Monad`实例的情况下，我们期望以下操作:`map`、`of`、`ap`和`chain`。

```
// fp-ts/IO.ts

export const io = {
  map: ...,
  of: ...,
  ap: ...,
  chain: ...
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们试着用同样的风格为`Task`写一个`time`组合子

## 起重

为了让类型检查器高兴，我们必须**将**具有类型`IO<number>`的`now`动作提升到`Task`单子。

幸运的是`fp-ts`为此提供了一个内置的`fromIO`函数。`fromIO`把一个`IO<A>`变成一个`Task<A>`，对于所有的`A`。

```
import { Task, task as M, fromIO } from 'fp-ts/Task'
import * as D from 'fp-ts/Date'

export function time<A>(ma: Task<A>): Task<[A, number]> {
  const now = fromIO(D.now)
  return M.chain(now, start => M.chain(ma, a => M.map(now, end => [a, end - start])))
} 
```

Enter fullscreen mode Exit fullscreen mode

那是可行的，但是有太多的重复。

如果我们暂时忽略正文的第一行，代码和之前完全一样，不是吗？

```
export function time<A>(ma: IO<A>): IO<[A, number]> {
  return M.chain(now, start => M.chain(ma, a => M.map(now, end => [a, end - start])))
}

export function time<A>(ma: Task<A>): Task<[A, number]> {
  return M.chain(now, start => M.chain(ma, a => M.map(now, end => [a, end - start])))
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是一元接口的美妙之处，我们可以用几乎相同的代码处理同步和异步计算。

## 无标签最终

所以这个想法是`time`组合子可以支持任何能够举起`now`的单子`M`。

或者，更一般地说，对于所有的`A`，任何能够将一个动作`IO<A>`提升为一个动作`M<A>`的单子`M`。

让我们将这样的能力编码到一个名为`MonadIO`
的类型类(即 TypeScript 中的`interface`

```
import { IO } from 'fp-ts/IO'
import { Monad1 } from 'fp-ts/Monad'
import { Kind, URIS } from 'fp-ts/HKT'

export interface MonadIO<M extends URIS> extends Monad1<M> {
  readonly fromIO: <A>(fa: IO<A>) => Kind<M, A>
} 
```

Enter fullscreen mode Exit fullscreen mode

类型`Kind<M, A>`是`fp-ts`如何编码种类`* -> *`的泛型类型构造函数`M<A>`(TypeScript 本身不支持更高种类的类型)。

让我们针对`MonadIO`接口重写`time`组合子(这种编程风格被称为“无标签 final”或“MTL 风格”)，作为附加的第一个参数
传入

```
export function time<M extends URIS>(
  M: MonadIO<M>
): <A>(ma: Kind<M, A>) => Kind<M, [A, number]> {
  const now = M.fromIO(D.now) // lifting
  return ma => M.chain(now, start => M.chain(ma, a => M.map(now, end => [a, end - start])))
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了，从现在开始，我们可以将`time`与任何一个接受`MonadIO`实例的单子一起使用了！

## 写一个`MonadIO`实例

为了给`IO`写一个`MonadIO`实例，我们必须用`fromIO`操作增加它的`Monad`实例，这就是`identity`函数

```
import { URI, io } from 'fp-ts/IO'
import { identity } from 'fp-ts/function'

export const monadIOIO: MonadIO<URI> = {
  ...io,
  fromIO: identity
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们为`Task`
写一个`MonadIO`实例

```
import { URI, task, fromIO } from 'fp-ts/Task'

const monadIOTask: MonadIO<URI> = {
  ...task,
  fromIO: fromIO
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以通过传递相应的`MonadIO`实例
来获得具体类型构造函数的专用版本`time`

```
// timeIO: <A>(ma: IO<A>) => IO<[A, number]>
const timeIO = time(monadIOIO)

// timeTask: <A>(ma: Task<A>) => Task<[A, number]>
const timeTask = time(monadIOTask) 
```

Enter fullscreen mode Exit fullscreen mode

这种方法有很大的好处:当编写一个基于无标签 final 的函数时，函数的目标单子可以在将来由用户更改。