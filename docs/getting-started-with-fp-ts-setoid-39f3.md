# fp-ts 入门:Eq

> 原文：<https://dev.to/gcanti/getting-started-with-fp-ts-setoid-39f3>

在这个博客系列中，我将经常谈到“类型类”和“实例”，让我们看看它们是什么以及它们是如何在`fp-ts`中编码的。

[【类型类】在维基百科上](https://en.wikipedia.org/wiki/Type_class)

> 程序员通过指定一组函数或常量名以及它们各自的类型来定义一个**类型类**，这些函数或常量名对于属于该类的每个类型都必须存在。

在`fp-ts`中，类型类被编码为类型脚本`interface`。

类型类`Eq`，旨在包含允许**相等**的类型，以下面的方式声明

```
interface Eq<A> {
  /** returns `true` if `x` is equal to `y` */
  readonly equals: (x: A, y: A) => boolean
} 
```

Enter fullscreen mode Exit fullscreen mode

该声明可以理解为

> 如果有适当类型的名为`equal`的函数，类型`A`属于类型类`Eq`,并在其上定义

那么**实例** s 呢？

> 程序员可以通过使用实例声明使任何类型`A`成为给定类型类`C`的成员，该实例声明为特定类型`A`定义了所有`C`成员的实现。

在`fp-ts`中，实例被编码为静态字典。

作为一个例子，这里是类型`number`
的`Eq`的实例

```
const eqNumber: Eq<number> = {
  equals: (x, y) => x === y
} 
```

Enter fullscreen mode Exit fullscreen mode

实例必须满足以下法律:

1.  **反身性** : `equals(x, x) === true`，对于`A`中的所有`x`
2.  **对称** : `equals(x, y) === equals(y, x)`，对于所有`x`，`A`中的`y`
3.  **传递性**:如果`equals(x, y) === true`和`equals(y, z) === true`，那么`equals(x, z) === true`，对于`A`中的所有`x`、`y`、`z`

然后程序员可以用下面的方式定义一个函数`elem`(它决定一个元素是否在一个数组中)

```
function elem<A>(E: Eq<A>): (a: A, as: Array<A>) => boolean {
  return (a, as) => as.some(item => E.equals(item, a))
}

elem(eqNumber)(1, [1, 2, 3]) // true
elem(eqNumber)(4, [1, 2, 3]) // false 
```

Enter fullscreen mode Exit fullscreen mode

让我们为更复杂的类型
编写一些`Eq`实例

```
type Point = {
  x: number
  y: number
}

const eqPoint: Eq<Point> = {
  equals: (p1, p2) => p1.x === p2.x && p1.y === p2.y
} 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至可以通过首先检查引用相等性
来优化`equals`

```
const eqPoint: Eq<Point> = {
  equals: (p1, p2) => p1 === p2 || (p1.x === p2.x && p1.y === p2.y)
} 
```

Enter fullscreen mode Exit fullscreen mode

不过，这基本上是样板文件。好消息是，如果我们可以为每个字段提供一个`Eq`实例，我们就可以为像`Point`这样的结构构建一个`Eq`实例。

的确，`fp-ts/Eq`模块导出了一个`getStructEq` [组合子](https://dev.to/gcanti/functional-design-combinators-14pn) :

```
import { getStructEq } from 'fp-ts/Eq'

const eqPoint: Eq<Point> = getStructEq({
  x: eqNumber,
  y: eqNumber
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以继续用刚刚定义的实例
来填充`getStructEq`

```
type Vector = {
  from: Point
  to: Point
}

const eqVector: Eq<Vector> = getStructEq({
  from: eqPoint,
  to: eqPoint
}) 
```

Enter fullscreen mode Exit fullscreen mode

`getStructEq`不是`fp-ts`提供的唯一组合子，这里有一个组合子允许为数组
派生一个`Eq`实例

```
import { getEq } from 'fp-ts/Array'

const eqArrayOfPoints: Eq<Array<Point>> = getEq(eqPoint) 
```

Enter fullscreen mode Exit fullscreen mode

最后，另一个构建`Eq`实例的有用方法是`contramap`组合子:给定一个`A`的`Eq`实例和一个从`B`到`A`的函数，我们可以为`B`
派生出一个`Eq`实例

```
import { contramap } from 'fp-ts/Eq'

type User = {
  userId: number
  name: string
}

/** two users are equal if their `userId` field is equal */
const eqUser = contramap((user: User) => user.userId)(eqNumber)

eqUser.equals({ userId: 1, name: 'Giulio' }, { userId: 1, name: 'Giulio Canti' }) // true
eqUser.equals({ userId: 1, name: 'Giulio' }, { userId: 2, name: 'Giulio' }) // false 
```

Enter fullscreen mode Exit fullscreen mode

下一篇 [Ord](https://dev.to/gcanti/getting-started-with-fp-ts-ord-5f1e)