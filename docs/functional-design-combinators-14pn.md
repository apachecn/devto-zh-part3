# 功能设计:组合子

> 原文：<https://dev.to/gcanti/functional-design-combinators-14pn>

在本文中，术语“组合子”指的是[组合子模式](https://wiki.haskell.org/Combinator)

> 一种以合并事物为中心的组织图书馆的方式。通常有一些类型`T`，一些类型`T`的“原始”值，以及一些“组合子”,它们可以以各种方式组合类型`T`的值，以构建类型`T`的更复杂的值

所以组合子的一般形状是

```
combinator: Thing -> Thing 
```

Enter fullscreen mode Exit fullscreen mode

组合子的目标是从先前定义的“事物”中创建新的“事物”。

因为结果可以作为输入传递回来，所以您得到了一个可能性的组合爆炸，这使得这个模式非常强大。

如果你把几个组合子混合在一起，你会得到一个更大的组合爆炸。

所以你可能经常在功能模块中发现的设计是

*   一小组非常简单的“原语”
*   一组“组合子”，用于将它们组合成更复杂的结构

让我们看一些例子。

## 例一:`Eq`

`getEq`组合子:给定`A`的一个`Eq`实例，我们可以导出`ReadonlyArray<A>`T5 的一个`Eq`实例

```
import { Eq, fromEquals } from 'fp-ts/Eq'

export function getEq<A>(E: Eq<A>): Eq<ReadonlyArray<A>> {
  return fromEquals(
    (xs, ys) =>
      xs.length === ys.length && xs.every((x, i) => E.equals(x, ys[i]))
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

用法

```
/** a primitive `Eq` instance for `number` */
export const eqNumber: Eq<number> = {
  equals: (x, y) => x === y
}

// derived
export const eqNumbers: Eq<ReadonlyArray<number>> = getEq(eqNumber)

// derived
export const eqNumbersNumbers: Eq<ReadonlyArray<ReadonlyArray<number>>> = getEq(
  eqNumbers
)

// derived
export const eqNumbersNumbersNumbers: Eq<ReadonlyArray<
  ReadonlyArray<ReadonlyArray<number>>
>> = getEq(eqNumbersNumbers)

// etc... 
```

Enter fullscreen mode Exit fullscreen mode

另一个组合子，`contramap`:给定`A`的一个`Eq`实例和一个从`B`到`A`的函数，我们可以导出`B`
的一个`Eq`实例

```
import { Eq, fromEquals } from 'fp-ts/Eq'

export const contramap = <A, B>(f: (b: B) => A) => (E: Eq<A>): Eq<B> =>
  fromEquals((x, y) => E.equals(f(x), f(y))) 
```

Enter fullscreen mode Exit fullscreen mode

用法

```
import { contramap, Eq } from 'fp-ts/Eq'
import { pipe } from 'fp-ts/function'
import * as N from 'fp-ts/number'
import * as RA from 'fp-ts/ReadonlyArray'

export interface User {
  id: number
  name: string
}

export const eqUser: Eq<User> = pipe(
  N.Eq,
  contramap((user: User) => user.id)
)

export const eqUsers: Eq<Array<User>> = RA.getEq(eqUser) 
```

Enter fullscreen mode Exit fullscreen mode

## 例二:`Monoid`

`getMonoid`组合子:给定`A`的一个`Monoid`实例，我们可以导出`IO<A>`T5 的一个`Monoid`实例

```
import { IO } from 'fp-ts/IO'
import { Monoid } from 'fp-ts/Monoid'

export function getMonoid<A>(M: Monoid<A>): Monoid<IO<A>> {
  return {
    concat: (x, y) => () => M.concat(x(), y()),
    empty: () => M.empty
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用`getMonoid`来派生另一个组合符`replicateIO`:给定一个数字`n`和一个`IO<void>`类型的动作`mv`，我们可以派生一个执行`n`次`mv`
的动作

```
import { concatAll } from 'fp-ts/Monoid'
import { replicate } from 'fp-ts/ReadonlyArray'

/** a primitive `Monoid` instance for `void` */
export const monoidVoid: Monoid<void> = {
  concat: () => undefined,
  empty: undefined
}

export function replicateIO(n: number, mv: IO<void>): IO<void> {
  return concatAll(getMonoid(monoidVoid))(replicate(n, mv))
} 
```

Enter fullscreen mode Exit fullscreen mode

用法

```
//
// helpers
//

/** logs to the console */
export function log(message: unknown): IO<void> {
  return () => console.log(message)
}

/** returns a random integer between `low` and `high` */
export const randomInt = (low: number, high: number): IO<number> => {
  return () => Math.floor((high - low + 1) * Math.random() + low)
}

//
// program
//
import { chain } from 'fp-ts/IO'
import { pipe } from 'fp-ts/function'

function fib(n: number): number {
  return n <= 1 ? 1 : fib(n - 1) + fib(n - 2)
}

/** calculates a random fibonacci and prints the result to the console */
const printFib: IO<void> = pipe(
  randomInt(30, 35),
  chain((n) => log(fib(n)))
)

replicateIO(3, printFib)()
/*
1346269
9227465
3524578
*/ 
```

Enter fullscreen mode Exit fullscreen mode

## 例三:`IO`

我们可以为`IO`构建许多其他组合子，例如`time`组合子模仿类似的 Unix 命令:给定一个动作`IO<A>`，我们可以派生出一个动作`IO<A>`，它将运行时间
打印到控制台

```
import { IO, Monad } from 'fp-ts/IO'
import { now } from 'fp-ts/Date'
import { log } from 'fp-ts/Console'

export function time<A>(ma: IO<A>): IO<A> {
  return Monad.chain(now, (start) =>
    Monad.chain(ma, (a) =>
      Monad.chain(now, (end) =>
        Monad.map(log(`Elapsed: ${end - start}`), () => a)
      )
    )
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

用法

```
time(replicateIO(3, printFib))()
/*
5702887
1346269
14930352
Elapsed: 193
*/ 
```

Enter fullscreen mode Exit fullscreen mode

有部分...

```
time(replicateIO(3, time(printFib)))()
/*
3524578
Elapsed: 32
14930352
Elapsed: 125
3524578
Elapsed: 32
Elapsed: 189
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我们能让`time`组合子更通用吗？我们将在[下一篇文章](https://dev.to/gcanti/functional-design-how-to-make-the-time-combinator-more-general-3fge)中看到。