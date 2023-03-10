# fp-ts 入门:Ord

> 原文：<https://dev.to/gcanti/getting-started-with-fp-ts-ord-5f1e>

在之前关于 [Eq](https://dev.to/gcanti/getting-started-with-fp-ts-setoid-39f3) 的博文中，我们讨论了**平等**的概念。在这篇博文中，我们将讨论**订单**的概念。

类型类`Ord`，旨在包含允许**总排序**的类型，以下面的方式声明

```
import { Eq } from 'fp-ts/Eq'

type Ordering = -1 | 0 | 1

interface Ord<A> extends Eq<A> {
  readonly compare: (x: A, y: A) => Ordering
} 
```

Enter fullscreen mode Exit fullscreen mode

我们说

*   当且仅当`compare(x, y)`等于`-1`
*   `x`等于`y`当且仅当`compare(x, y)`等于`0`
*   当且仅当`compare(x, y)`等于`1`

因此，我们可以说`x <= y`当且仅当`compare(x, y) <= 0`

作为一个例子，这里是类型`number`
的`Ord`的实例

```
const ordNumber: Ord<number> = {
  equals: (x, y) => x === y,
  compare: (x, y) => (x < y ? -1 : x > y ? 1 : 0)
} 
```

Enter fullscreen mode Exit fullscreen mode

实例必须满足以下法律:

1.  **反身性** : `compare(x, x) === 0`，对于`A`中的所有`x`
2.  **反对称**:如果`compare(x, y) <= 0`和`compare(y, x) <= 0`那么`x`等于`y`，对于`A`中的所有`x`、`y`
3.  **传递性**:如果`compare(x, y) <= 0`和`compare(y, z) <= 0`则`compare(x, z) <= 0`，对于`A`中的所有`x`、`y`、`z`

另外`compare`必须符合`Eq`的`equals`:

`compare(x, y) === 0`当且仅当`equals(x, y) === true`，对于所有`x`、`A`中的`y`

注意。合法的`equals`可以通过以下方式从`compare`中导出

```
equals: (x, y) => compare(x, y) === 0 
```

Enter fullscreen mode Exit fullscreen mode

事实上，模块`fp-ts/Ord`导出了一个方便的`fromCompare`助手，它允许你通过简单地指定一个`compare`函数
来定义一个`Ord`实例

```
import { Ord, fromCompare } from 'fp-ts/Ord'

const ordNumber: Ord<number> = fromCompare((x, y) => (x < y ? -1 : x > y ? 1 : 0)) 
```

Enter fullscreen mode Exit fullscreen mode

然后程序员可以用下面的方式定义一个函数`min`(取两个值中的最小值)

```
function min<A>(O: Ord<A>): (x: A, y: A) => A {
  return (x, y) => (O.compare(x, y) === 1 ? y : x)
}

min(ordNumber)(2, 1) // 1 
```

Enter fullscreen mode Exit fullscreen mode

**当我们谈论数字时，总体**可能看起来很明显(即`x <= y`或`y <= x`)，但情况并非总是如此。让我们考虑一个更复杂的类型

```
type User = {
  name: string
  age: number
} 
```

Enter fullscreen mode Exit fullscreen mode

我们如何定义一个`Ord<User>`？

这真的要视情况而定，但是一个可能的选择是按照用户的`age`
进行分类

```
const byAge: Ord<User> = fromCompare((x, y) => ordNumber.compare(x.age, y.age)) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过使用`contramap` [组合符](https://dev.to/gcanti/functional-design-combinators-14pn)来避免一些样板文件:给定一个用于`A`的`Ord`实例和一个从`B`到`A`的函数，我们可以为`B`
派生出一个`Ord`实例

```
import { contramap } from 'fp-ts/Ord'

const byAge: Ord<User> = contramap((user: User) => user.age)(ordNumber) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用`min`
选择两个用户中较年轻的一个

```
const getYounger = min(byAge)

getYounger({ name: 'Guido', age: 48 }, { name: 'Giulio', age: 45 }) // { name: 'Giulio', age: 45 } 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想选择年长的呢？我们需要“颠倒顺序”，或者更专业地说，得到双**顺序。**

幸运的是，这个
有另一个导出的组合子

```
import { getDualOrd } from 'fp-ts/Ord'

function max<A>(O: Ord<A>): (x: A, y: A) => A {
  return min(getDualOrd(O))
}

const getOlder = max(byAge)

getOlder({ name: 'Guido', age: 48 }, { name: 'Giulio', age: 45 }) // { name: 'Guido', age: 48 } 
```

Enter fullscreen mode Exit fullscreen mode

下一篇文章[半群](https://dev.to/gcanti/getting-started-with-fp-ts-semigroup-2mf7)