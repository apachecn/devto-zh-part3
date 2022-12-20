# fp-ts 入门:半群

> 原文：<https://dev.to/gcanti/getting-started-with-fp-ts-semigroup-2mf7>

由于半群是函数式编程的一个基本抽象，这篇博文将会比平常更长。

## 一般定义

半群是一对`(A, *)`，其中`A`是非空集，`*`是对`A`的二元**结合**运算，即以`A`的两个元素作为输入，返回`A`的一个元素作为输出的函数...

```
*: (x: A, y: A) => A 
```

Enter fullscreen mode Exit fullscreen mode

...而关联意味着等式

```
(x * y) * z = x * (y * z) 
```

Enter fullscreen mode Exit fullscreen mode

适用于`A`中的所有`x`、`y`、`z`。

结合律只是告诉我们不必担心表达式的括号，并且可以写`x * y * z`。

> 半群抓住了可并行操作的本质

有很多半群的例子:

*   `(number, *)`其中`*`是通常的数字乘法
*   `(string, +)`其中`+`是通常的字符串连接
*   `(boolean, &&)`其中`&&`是通常的连词

还有很多。

## 类型类定义

在`fp-ts`中，包含在`fp-ts/Semigroup`模块中的类型类`Semigroup`通常被实现为类型脚本`interface`，其中操作`*`被命名为`concat`

```
interface Semigroup<A> {
  concat: (x: A, y: A) => A
} 
```

Enter fullscreen mode Exit fullscreen mode

以下定律必须成立

*   **关联性**:T0，`A`中的所有`x`、`y`、`z`

名称`concat`对数组有特殊的意义(见后面),但是，基于上下文和类型`A`(我们正在为其实现一个实例),半群运算可以有不同的含义

*   “串联”
*   "合并"
*   “融合”
*   "选择"
*   “加法”
*   “替代”

还有很多。

## 实例

这就是我们如何实现半群`(number, *)`

```
/** number `Semigroup` under multiplication */
const semigroupProduct: Semigroup<number> = {
  concat: (x, y) => x * y
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您可以为同一类型定义不同的半群实例。这里是半群`(number, +)`的实现，其中`+`是数字
的通常加法

```
/** number `Semigroup` under addition */
const semigroupSum: Semigroup<number> = {
  concat: (x, y) => x + y
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个例子，这次用的是字符串

```
const semigroupString: Semigroup<string> = {
  concat: (x, y) => x + y
} 
```

Enter fullscreen mode Exit fullscreen mode

## 找不到实例！

如果给定一个类型`A`，在`A`上找不到关联操作怎么办？你可以为每种类型创建一个(平凡的)半群实例，只需使用下面的构造

```
/** Always return the first argument */
function getFirstSemigroup<A = never>(): Semigroup<A> {
  return { concat: (x, y) => x }
}

/** Always return the second argument */
function getLastSemigroup<A = never>(): Semigroup<A> {
  return { concat: (x, y) => y }
} 
```

Enter fullscreen mode Exit fullscreen mode

另一种技术是为`Array<A>` (*)定义一个半群实例，称为`A`的**自由半群**。

```
function getArraySemigroup<A = never>(): Semigroup<Array<A>> {
  return { concat: (x, y) => x.concat(y) }
} 
```

Enter fullscreen mode Exit fullscreen mode

并将`A`的元素映射到`Array<A>`
的单元素

```
function of<A>(a: A): Array<A> {
  return [a]
} 
```

Enter fullscreen mode Exit fullscreen mode

(*)严格来说是半群实例对于非空数组`A`

**注**。这里的`concat`是原生数组方法，它解释了最初对`Semigroup`操作名称的选择。

`A`的自由半群是其元素为`A`的元素的所有可能的非空有限序列的半群。

## 源自`Ord`

还有另一种方法来为类型`A`构建一个半群实例:如果我们已经有了`A`的 [Ord](https://dev.to/gcanti/getting-started-with-fp-ts-ord-5f1e) 实例，那么我们可以将它“转变”成一个半群。

实际上**两个**可能半群

```
import { ordNumber } from 'fp-ts/Ord'
import { getMeetSemigroup, getJoinSemigroup } from 'fp-ts/Semigroup'

/** Takes the minimum of two values */
const semigroupMin: Semigroup<number> = getMeetSemigroup(ordNumber)

/** Takes the maximum of two values  */
const semigroupMax: Semigroup<number> = getJoinSemigroup(ordNumber)

semigroupMin.concat(2, 1) // 1
semigroupMax.concat(2, 1) // 2 
```

Enter fullscreen mode Exit fullscreen mode

让我们为更复杂的类型
编写一些`Semigroup`实例

```
type Point = {
  x: number
  y: number
}

const semigroupPoint: Semigroup<Point> = {
  concat: (p1, p2) => ({
    x: semigroupSum.concat(p1.x, p2.x),
    y: semigroupSum.concat(p1.y, p2.y)
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

不过，这基本上是样板文件。好消息是，如果我们可以为每个字段提供一个`Semigroup`实例，我们就可以为像`Point`这样的结构构建一个`Semigroup`实例。

的确，`fp-ts/Semigroup`模块导出了一个`getStructSemigroup` [组合子](https://dev.to/gcanti/functional-design-combinators-14pn) :

```
import { getStructSemigroup } from 'fp-ts/Semigroup'

const semigroupPoint: Semigroup<Point> = getStructSemigroup({
  x: semigroupSum,
  y: semigroupSum
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以继续用刚刚定义的实例
来填充`getStructSemigroup`

```
type Vector = {
  from: Point
  to: Point
}

const semigroupVector: Semigroup<Vector> = getStructSemigroup({
  from: semigroupPoint,
  to: semigroupPoint
}) 
```

Enter fullscreen mode Exit fullscreen mode

`getStructSemigroup`不是`fp-ts`提供的唯一组合子，这里有一个组合子允许为函数派生一个`Semigroup`实例:给定一个`S`的`Semigroup`实例，我们可以为所有`A`
函数派生一个`Semigroup`实例

```
import { getFunctionSemigroup, Semigroup, semigroupAll } from 'fp-ts/Semigroup'

/** `semigroupAll` is the boolean semigroup under conjunction */
const semigroupPredicate: Semigroup<(p: Point) => boolean> = getFunctionSemigroup(
  semigroupAll
)<Point>() 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以“合并”在`Point` s
上的两个谓词

```
const isPositiveX = (p: Point): boolean => p.x >= 0
const isPositiveY = (p: Point): boolean => p.y >= 0

const isPositiveXY = semigroupPredicate.concat(isPositiveX, isPositiveY)

isPositiveXY({ x: 1, y: 1 }) // true
isPositiveXY({ x: 1, y: -1 }) // false
isPositiveXY({ x: -1, y: 1 }) // false
isPositiveXY({ x: -1, y: -1 }) // false 
```

Enter fullscreen mode Exit fullscreen mode

## 折叠

根据定义，`concat`只能处理`A`的两个元素，如果我们想要连接更多的元素呢？

`fold`函数接受一个半群实例、一个初始值和一个元素数组:

```
import { fold, semigroupSum, semigroupProduct } from 'fp-ts/Semigroup'

const sum = fold(semigroupSum)

sum(0, [1, 2, 3, 4]) // 10

const product = fold(semigroupProduct)

product(1, [1, 2, 3, 4]) // 24 
```

Enter fullscreen mode Exit fullscreen mode

## 半群为类型构造子

如果要“合并”两个`Option<A>`呢？有四种情况:

| x | y | concat(x，y) |
| --- | --- | --- |
| 没有人 | 没有人 | 没有人 |
| 一些(一) | 没有人 | 没有人 |
| 没有人 | 一些(一) | 没有人 |
| 一些(一) | 一些(b) | ？ |

最后一个有问题，我们需要一些东西来“合并”两个`A`。

那就是`Semigroup`做的！我们可以为`A`要求一个半群实例，然后为`Option<A>`导出一个半群实例。这就是`getApplySemigroup`组合子的工作方式

```
import { semigroupSum } from 'fp-ts/Semigroup'
import { getApplySemigroup, some, none } from 'fp-ts/Option'

const S = getApplySemigroup(semigroupSum)

S.concat(some(1), none) // none
S.concat(some(1), some(2)) // some(3) 
```

Enter fullscreen mode Exit fullscreen mode

## 附录

我们已经看到，当我们想要将几个数据“连接”、“合并”或“组合”(无论什么词给你最好的直觉)成一个数据时，半群随时会帮助我们。

让我们用最后一个例子总结一下(改编自 [Fantas、Eel 和规范 4:半群](http://www.tomharding.me/2017/03/13/fantas-eel-and-specification-4/))

让我们假设您正在构建一个系统，在这个系统中，您存储的客户记录如下所示:

```
interface Customer {
  name: string
  favouriteThings: Array<string>
  registeredAt: number // since epoch
  lastUpdatedAt: number // since epoch
  hasMadePurchase: boolean
} 
```

Enter fullscreen mode Exit fullscreen mode

不管出于什么原因，你都可能会得到同一个人的重复记录。我们需要的是一个合并策略。这就是半群的意义所在

```
import {
  Semigroup,
  getStructSemigroup,
  getJoinSemigroup,
  getMeetSemigroup,
  semigroupAny
} from 'fp-ts/Semigroup'
import { getMonoid } from 'fp-ts/Array'
import { ordNumber, contramap } from 'fp-ts/Ord'

const semigroupCustomer: Semigroup<Customer> = getStructSemigroup({
  // keep the longer name
  name: getJoinSemigroup(contramap((s: string) => s.length)(ordNumber)),
  // accumulate things
  favouriteThings: getMonoid<string>(), // <= getMonoid returns a Semigroup for `Array<string>` see later
  // keep the least recent date
  registeredAt: getMeetSemigroup(ordNumber),
  // keep the most recent date
  lastUpdatedAt: getJoinSemigroup(ordNumber),
  // Boolean semigroup under disjunction
  hasMadePurchase: semigroupAny
})

semigroupCustomer.concat(
  {
    name: 'Giulio',
    favouriteThings: ['math', 'climbing'],
    registeredAt: new Date(2018, 1, 20).getTime(),
    lastUpdatedAt: new Date(2018, 2, 18).getTime(),
    hasMadePurchase: false
  },
  {
    name: 'Giulio Canti',
    favouriteThings: ['functional programming'],
    registeredAt: new Date(2018, 1, 22).getTime(),
    lastUpdatedAt: new Date(2018, 2, 9).getTime(),
    hasMadePurchase: true
  }
)
/*
{ name: 'Giulio Canti',
  favouriteThings: [ 'math', 'climbing', 'functional programming' ],
  registeredAt: 1519081200000, // new Date(2018, 1, 20).getTime()
  lastUpdatedAt: 1521327600000, // new Date(2018, 2, 18).getTime()
  hasMadePurchase: true }
*/ 
```

Enter fullscreen mode Exit fullscreen mode

函数`getMonoid`为`Array<string>`返回一个`Semigroup`。实际上，它返回的不仅仅是一个半群:一个**幺半群**。

那么幺半群是什么？在下一篇文章中，我将谈论[幺半群](https://dev.to/gcanti/getting-started-with-fp-ts-monoid-ja0)