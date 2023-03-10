# fp-ts: IO 入门

> 原文：<https://dev.to/gcanti/getting-started-with-fp-ts-io-36p6>

在`fp-ts`中，同步有效计算由`IO`类型表示，它基本上是一个 **thunk** ，即一个具有以下签名的函数:`() => A`

```
interface IO<A> {
  (): A
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，`IO`代表一个**不会让**失败的计算。

这种计算的例子有:

*   读取/写入`localStorage`
*   获取当前时间
*   写入到`console`
*   得到一个随机数

**示例**(读/写至`localStorage` )

```
import { fromNullable, Option } from 'fp-ts/Option'

const getItem = (key: string): IO<Option<string>> => () =>
  fromNullable(localStorage.getItem(key))

const setItem = (key: string, value: string): IO<void> => () =>
  localStorage.setItem(key, value) 
```

Enter fullscreen mode Exit fullscreen mode

**示例**(获取当前时间)

```
const now: IO<number> = () => new Date().getTime() 
```

Enter fullscreen mode Exit fullscreen mode

**例**(写到`console` )

```
const log = (s: unknown): IO<void> => () => console.log(s) 
```

Enter fullscreen mode Exit fullscreen mode

**例子**(得到一个随机数)

```
const random: IO<number> = () => Math.random() 
```

Enter fullscreen mode Exit fullscreen mode

`IO`类型承认一个[单子](https://dev.to/gcanti/getting-started-with-fp-ts-monad-6k)实例，所以你可以`map`...

```
import { io } from 'fp-ts/IO'

/** get a random boolean */
const randomBool: IO<boolean> = io.map(random, n => n < 0.5) 
```

Enter fullscreen mode Exit fullscreen mode

...或`chain`计算

```
/** write to the `console` a random boolean */
const program: IO<void> = io.chain(randomBool, log)

program() 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在您调用`program()`之前**不会发生任何事情**。

那是因为`program`是一个**值**，它只是**代表**一个有效的计算，所以为了执行任何副作用，你必须“运行`IO`动作”。

因为`IO`动作只是值，所以你可以使用像[幺半群](https://dev.to/gcanti/getting-started-with-fp-ts-monoid-ja0)这样有用的抽象来处理它们...

**举例**(龙与地下城)

```
import { log } from 'fp-ts/Console'
import { getMonoid, IO, io } from 'fp-ts/IO'
import { fold, Monoid, monoidSum } from 'fp-ts/Monoid'
import { randomInt } from 'fp-ts/Random'

type Die = IO<number>

const monoidDie: Monoid<Die> = getMonoid(monoidSum)

/** returns the sum of the roll of the dice */
const roll: (dice: Array<Die>) => IO<number> = fold(monoidDie)

const D4: Die = randomInt(1, 4)
const D10: Die = randomInt(1, 10)
const D20: Die = randomInt(1, 20)

const dice = [D4, D10, D20]

io.chain(roll(dice), result => log(`Result is: ${result}`))()
/*
Result is: 11
*/ 
```

Enter fullscreen mode Exit fullscreen mode

..或者定义有用的[组合子](https://dev.to/gcanti/functional-design-combinators-14pn)

```
/** Log any value to the console for debugging purposes */
const withLogging = <A>(action: IO<A>): IO<A> =>
  io.chain(action, a => io.map(log(`Value is: ${a}`), () => a))

io.chain(roll(dice.map(withLogging)), result => log(`Result is: ${result}`))()
/*
Value is: 4
Value is: 2
Value is: 13
Result is: 19
*/ 
```

Enter fullscreen mode Exit fullscreen mode

# 错误处理

如果我们想表示一个同步有效的计算，而**可能会使**失败，该怎么办？

我们需要两种效果:

| 类型构造函数 | 效果(解释) |
| --- | --- |
| `IO<A>` | 同步有效计算 |
| `Either<E, A>` | 可能失败的计算 |

解决方法是把`Either`放在`IO`里面，这就产生了`IOEither`类型

```
interface IOEither<E, A> extends IO<Either<E, A>> {} 
```

Enter fullscreen mode Exit fullscreen mode

当我们“运行”一个类型为`IOEither<E, A>`的值时，如果我们得到一个`Left`,这意味着计算因类型为`E`的错误而失败，否则我们得到一个`Right`,这意味着计算因类型为`A`的值而成功。

**示例**(读取一个文件)

既然`fs.readFileSync`可能会扔，我打算用`tryCatch`帮手

```
tryCatch: <E, A>(f: () => A) => IOEither<E, A> 
```

Enter fullscreen mode Exit fullscreen mode

其中`f`是一个 thunk，它要么抛出一个错误(由`tryCatch`自动捕获)，要么返回一个类型为`A`的值。

```
import { toError } from 'fp-ts/Either'
import { IOEither, tryCatch } from 'fp-ts/IOEither'
import * as fs from 'fs'

const readFileSync = (path: string): IOEither<Error, string> =>
  tryCatch(() => fs.readFileSync(path, 'utf8'), toError)

readFileSync('foo')() // => left(Error: ENOENT: no such file or directory, open 'foo')
readFileSync(__filename)() // => right(...) 
```

Enter fullscreen mode Exit fullscreen mode

# 起重

`fp-ts/IOEither`模块提供了允许创建`IOEither`类型值的其他助手，它们统称为**提升函数**。

这里有一个总结

| 起始值 | 提升功能 |
| --- | --- |
| `IO<E>` | `leftIO: <E, A>(ml: IO<E>) => IOEither<E, A>` |
| `E` | `left: <E, A>(e: E) => IOEither<E, A>` |
| `Either<E, A>` | `fromEither: <E, A>(ma: Either<E, A>) => IOEither<E, A>` |
| `A` | `right: <E, A>(a: A) => IOEither<E, A>` |
| `IO<A>` | `rightIO: <E, A>(ma: IO<A>) => IOEither<E, A>` |

**示例**(加载随机文件)

假设我们想要随机加载三个文件之一的内容(`1.txt`、`2.txt`、`3.txt`)。

`randomInt: (low: number, high: number) => IO<number>`函数返回一个均匀分布在封闭区间`[low, high]`
的随机整数

```
import { randomInt } from 'fp-ts/Random' 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用上面定义的
的`readFileSync`函数来链接`randomInt`

```
import { ioEither } from 'fp-ts/IOEither'

const randomFile = ioEither.chain(
  randomInt(1, 3), // static error
  n => readFileSync(`${__dirname}/${n}.txt`)
) 
```

Enter fullscreen mode Exit fullscreen mode

但是这并不进行类型检查！

类型不对齐:`randomInt` *在`IO`上下文*中运行，而`readFileSync` *在`IOEither`上下文*中运行。

然而，我们可以通过使用`rightIO`(参见上面的总结)
将`randomInt`提升到`IOEither`上下文

```
import { ioEither, rightIO } from 'fp-ts/IOEither'

const randomFile = ioEither.chain(rightIO(randomInt(1, 3)), n =>
  readFileSync(`${__dirname}/${n}.txt`)
) 
```

Enter fullscreen mode Exit fullscreen mode