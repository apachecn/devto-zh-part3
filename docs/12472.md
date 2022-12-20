# 功能设计:如何让“时间”组合器更通用

> 原文：<https://dev.to/gcanti/functional-design-how-to-make-the-time-combinator-more-general-3fge>

在[的上一篇文章](https://dev.to/gcanti/functional-design-combinators-14pn)中，我写了一个`time`组合子，它模仿了类似的 Unix 命令:给定一个动作`IO<A>`，我们可以派生出一个动作`IO<A>`，它将运行时间
打印到控制台

```
import { IO, io } from 'fp-ts/IO'
import { now } from 'fp-ts/Date'
import { log } from 'fp-ts/Console'

export function time<A>(ma: IO<A>): IO<A> {
  return io.chain(now, start =>
    io.chain(ma, a => io.chain(now, end => io.map(log(`Elapsed: ${end - start}`), () => a)))
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

不过这个组合器有两个问题:

*   不灵活，即消费者无法选择如何处理过去的时间
*   仅适用于`IO`

在本文中，我们将解决第一个问题。

## 通过返回经过时间来增加灵活性

我们可以返回经过的时间以及计算出的值
，而不是总是记录日志

```
export function time<A>(ma: IO<A>): IO<[A, number]> {
  return io.chain(now, start => io.chain(ma, a => io.map(now, end => [a, end - start])))
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，用户可以通过定义自己的组合子来选择如何处理经过的时间。

我们仍然可以登录控制台...

```
export function withLogging<A>(ma: IO<A>): IO<A> {
  return io.chain(time(ma), ([a, millis]) =>
    io.map(log(`Result: ${a}, Elapsed: ${millis}`), () => a)
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

用法

```
import { randomInt } from 'fp-ts/Random'

function fib(n: number): number {
  return n <= 1 ? 1 : fib(n - 1) + fib(n - 2)
}

const program = withLogging(io.map(randomInt(30, 35), fib))

program()
/*
Result: 14930352, Elapsed: 127
*/ 
```

Enter fullscreen mode Exit fullscreen mode

...或者忽略过去的时间...

```
export function ignoreSnd<A>(ma: IO<[A, unknown]>): IO<A> {
  return io.map(ma, ([a]) => a)
} 
```

Enter fullscreen mode Exit fullscreen mode

...或者，例如，只保留非空列表中最快的动作

```
import { fold, getMeetSemigroup } from 'fp-ts/Semigroup'
import { contramap, ordNumber } from 'fp-ts/Ord'
import { getSemigroup } from 'fp-ts/IO'

export function fastest<A>(head: IO<A>, tail: Array<IO<A>>): IO<A> {
  const ordTuple = contramap(([_, elapsed]: [A, number]) => elapsed)(ordNumber)
  const semigroupTuple = getMeetSemigroup(ordTuple)
  const semigroupIO = getSemigroup(semigroupTuple)
  const fastest = fold(semigroupIO)(time(head), tail.map(time))
  return ignoreSnd(fastest)
} 
```

Enter fullscreen mode Exit fullscreen mode

用法

```
io.chain(fastest(program, [program, program]), a => log(`Fastest result is: ${a}`))()
/*
Result: 5702887, Elapsed: 49
Result: 2178309, Elapsed: 20
Result: 5702887, Elapsed: 57
Fastest result is: 2178309
*/ 
```

Enter fullscreen mode Exit fullscreen mode

在下一篇文章中，我们将通过引入一种强大的编程风格来解决第二个问题:无标签 final。

## 附录

`fastest`的实现相当密集，我们来看看相关的位:

1)它的签名确保我们提供一个非空的动作列表

```
//  at least one action --v            v--- possibly other actions
function fastest<A>(head: IO<A>, tail: Array<IO<A>>): IO<A> 
```

Enter fullscreen mode Exit fullscreen mode

2) `contramap`是一个`Ord`组合子:给定`T`的一个`Ord`实例和一个从`U`到`T`的函数，我们可以导出`U`的一个`Ord`实例。

这里的`T = number`和`U = [A, number]`

```
// from `Ord<number>` to `Ord<[A, number]>`
const ordTuple = contramap(([_, elapsed]: [A, number]) => elapsed)(ordNumber) 
```

Enter fullscreen mode Exit fullscreen mode

3) `getMeetSemigroup`将`Ord<T>`的实例转换为`Semigroup<T>`的实例，当组合两个值时，返回较小的

```
// from `Ord<[A, number]>` to `Semigroup<[A, number]>`
const semigroupTuple = getMeetSemigroup(ordTuple) 
```

Enter fullscreen mode Exit fullscreen mode

4) `getSemigroup`是一个`Semigroup`组合子:给定`T`的一个`Semigroup`实例，我们可以导出`IO<T>`
的一个`Semigroup`实例

```
// from `Semigroup<[A, number]>` to `Semigroup<IO<[A, number]>>`
const semigroupIO = getSemigroup(semigroupTuple) 
```

Enter fullscreen mode Exit fullscreen mode

5) `fold`使用提供的`Semigroup`
减少一个非空的动作列表

```
// from a non empty list of `IO<[A, number]>` to `IO<[A, number]>`
const fastest = fold(semigroupIO)(time(head), tail.map(time)) 
```

Enter fullscreen mode Exit fullscreen mode

6)最后，我们忽略经过的时间，只返回值

```
// from `IO<[A, number]>` to `IO<A>`
return ignoreSnd(fastest) 
```

Enter fullscreen mode Exit fullscreen mode