# fp-ts 入门:适用

> 原文：<https://dev.to/gcanti/getting-started-with-fp-ts-applicative-1kb3>

在[的上一篇](https://dev.to/gcanti/getting-started-with-fp-ts-functor-36ek)文章中，我们看到，只要`F`接受一个仿函数实例，通过将`g`提升到一个函数`lift(g): (fb: F<B>) => F<C>`，我们可以用一个纯程序`g: (b: B) => C`组成一个有效的程序`f: (a: A) => F<B>`

| 程序 | 程序 | 作文 |
| --- | --- | --- |
| 纯的 | 纯的 | `g ∘ f` |
| 有效的 | 纯(一元) | `lift(g) ∘ f` |

然而`g`必须是一元的，也就是说它必须只接受一个参数作为输入。如果`g`接受两个参数呢？我们还能通过仅仅使用仿函数实例来提升`g`吗？好吧，我们试试！

# 逢迎

首先，我们必须建模一个函数，它接受两个参数，比如说类型为`B`和`C`(我们可以使用一个元组)，并返回类型为`D`和
的值

```
g: (args: [B, C]) => D 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用一种叫做**奉承**的技术重写`g`。

> Currying 是一种将带有多个参数的函数的求值转化为一系列函数的求值的技术，**每个函数只有一个参数**。例如，一个函数有两个参数，一个来自`B`，一个来自`C`，并通过 currying 在`D`中产生输出，这个函数被转换成一个函数，它从`C`中获得一个参数，并产生从`B`到`C`的输出函数。

(来源:[在 wikipedia.org](https://en.wikipedia.org/wiki/Currying)拍马屁)

所以我们可以把`g`改写成

```
g: (b: B) => (c: C) => D 
```

Enter fullscreen mode Exit fullscreen mode

我们想要的是一个提升操作，姑且称之为`liftA2`以区别于我们旧的`lift`，它输出一个带有如下签名的函数

```
liftA2(g): (fb: F<B>) => (fc: F<C>) => F<D> 
```

Enter fullscreen mode Exit fullscreen mode

我们怎样才能到达那里？由于`g`现在是一元的，我们可以使用函子实例和我们旧的`lift`

```
lift(g): (fb: F<B>) => F<(c: C) => D> 
```

Enter fullscreen mode Exit fullscreen mode

但是现在我们陷入了困境:在函子实例上没有合法的操作能够**将值`F<(c: C) => D>`解包**到函数`(fc: F<C>) => F<D>`。

# 适用

所以让我们引入一个新的抽象`Apply`，它拥有这样一个解包操作(名为`ap` )

```
interface Apply<F> extends Functor<F> {
  ap: <C, D>(fcd: HKT<F, (c: C) => D>, fc: HKT<F, C>) => HKT<F, D>
} 
```

Enter fullscreen mode Exit fullscreen mode

`ap`函数基本上是`unpack`,参数经过了重新排列

```
unpack: <C, D>(fcd: HKT<F, (c: C) => D>) => ((fc: HKT<F, C>) => HKT<F, D>)
ap:     <C, D>(fcd: HKT<F, (c: C) => D>, fc: HKT<F, C>) => HKT<F, D> 
```

Enter fullscreen mode Exit fullscreen mode

所以`ap`可以从`unpack`推导出来(反之亦然)。

**注意**:`HKT`类型是表示泛型类型构造函数的`fp-ts`方式(在[轻量级高级多态](https://www.cl.cam.ac.uk/~jdy22/papers/lightweight-higher-kinded-polymorphism.pdf)论文中提出的一种技术)，所以当你看到`HKT<F, X>`时，你可以想到应用于类型`X`的类型构造函数`F`(即`F<X>`)。

# 适用

此外，如果存在一个能够将类型为`A`的值提升为类型为`F<A>`的值的操作，将会非常方便。这样，我们可以通过提供类型为`F<B>`和`F<C>`的参数或者提升类型为`B`和`C`的值来调用`liftA2(g)`函数。

所以让我们来介绍一下建立在`Apply`之上的`Applicative`抽象，它拥有这样的操作(名为`of` )

```
interface Applicative<F> extends Apply<F> {
  of: <A>(a: A) => HKT<F, A>
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看一些常见数据类型的`Applicative`实例

**举例** ( `F = Array` )

```
import { flatten } from 'fp-ts/Array'

const applicativeArray = {
  map: <A, B>(fa: Array<A>, f: (a: A) => B): Array<B> => fa.map(f),
  of: <A>(a: A): Array<A> => [a],
  ap: <A, B>(fab: Array<(a: A) => B>, fa: Array<A>): Array<B> =>
    flatten(fab.map(f => fa.map(f)))
} 
```

Enter fullscreen mode Exit fullscreen mode

**举例** ( `F = Option` )

```
import { Option, some, none, isNone } from 'fp-ts/Option'

const applicativeOption = {
  map: <A, B>(fa: Option<A>, f: (a: A) => B): Option<B> =>
    isNone(fa) ? none : some(f(fa.value)),
  of: <A>(a: A): Option<A> => some(a),
  ap: <A, B>(fab: Option<(a: A) => B>, fa: Option<A>): Option<B> =>
    isNone(fab) ? none : applicativeOption.map(fa, fab.value)
} 
```

Enter fullscreen mode Exit fullscreen mode

**举例** ( `F = Task` )

```
import { Task } from 'fp-ts/Task'

const applicativeTask = {
  map: <A, B>(fa: Task<A>, f: (a: A) => B): Task<B> => () => fa().then(f),
  of: <A>(a: A): Task<A> => () => Promise.resolve(a),
  ap: <A, B>(fab: Task<(a: A) => B>, fa: Task<A>): Task<B> => () =>
    Promise.all([fab(), fa()]).then(([f, a]) => f(a))
} 
```

Enter fullscreen mode Exit fullscreen mode

# 起重

那么给定一个`F`的`Apply`实例，我们现在可以写`liftA2`了吗？

```
import { HKT } from 'fp-ts/HKT'
import { Apply } from 'fp-ts/Apply'

type Curried2<B, C, D> = (b: B) => (c: C) => D

function liftA2<F>(
  F: Apply<F>
): <B, C, D>(g: Curried2<B, C, D>) => Curried2<HKT<F, B>, HKT<F, C>, HKT<F, D>> {
  return g => fb => fc => F.ap(F.map(fb, g), fc)
} 
```

Enter fullscreen mode Exit fullscreen mode

不错！但是有三个参数的函数呢？我们还需要*另一个抽象*吗？

好消息是答案是否定的，`Apply`足够了

```
type Curried3<B, C, D, E> = (b: B) => (c: C) => (d: D) => E

function liftA3<F>(
  F: Apply<F>
): <B, C, D, E>(
  g: Curried3<B, C, D, E>
) => Curried3<HKT<F, B>, HKT<F, C>, HKT<F, D>, HKT<F, E>> {
  return g => fb => fc => fd => F.ap(F.ap(F.map(fb, g), fc), fd)
} 
```

Enter fullscreen mode Exit fullscreen mode

实际上给定一个`Apply`的实例，我们可以为每个 `n`写一个`liftAn`函数、**。**

**注**。`liftA1`只是`lift`，`Functor`的操作。

我们现在可以更新我们的“成分表”

| 程序 | 程序 | 作文 |
| --- | --- | --- |
| 纯的 | 纯的 | `g ∘ f` |
| 有效的 | pure, `n` -ary | `liftAn(g) ∘ f` |

<center>where `liftA1 = lift`</center>

# 一般问题解决了吗？

还没有。还有一个重要的情况被遗漏了:如果两个程序都有效呢？

我们还需要更多的东西:在下一篇文章中，我将谈论函数式编程最重要的抽象之一:**单子**。

TLDR:函数式编程是关于组合的