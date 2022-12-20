# fp-ts 入门:幺半群

> 原文：<https://dev.to/gcanti/getting-started-with-fp-ts-monoid-ja0>

在上一篇[文章](https://dev.to/gcanti/getting-started-with-fp-ts-semigroup-2mf7)中，我们看到`Semigroup`抓住了“合并”值的概念(通过`concat`)。一个`Monoid`是任何一个碰巧有一个特殊值的`Semigroup`，这个值相对于`concat`是“中性”的。

## 类型类定义

像往常一样，在`fp-ts`中，包含在`fp-ts/Monoid`模块中的类型类`Monoid`被实现为类型脚本`interface`，其中中性值被命名为`empty`T5】

```
import { Semigroup } from 'fp-ts/Semigroup'

interface Monoid<A> extends Semigroup<A> {
  readonly empty: A
} 
```

Enter fullscreen mode Exit fullscreen mode

以下定律必须成立

*   **正确身份** : `concat(x, empty) = x`，对于`A`中的所有`x`
*   **左标识** : `concat(empty, x) = x`，为`A`中的所有`x`

无论我们将值`empty`放在`concat`的哪一边，它对值都没有影响。

**注**。如果`empty`值存在，那么*是唯一的*。

## 实例

我们在上一篇文章中看到的大多数半群实际上都是幺半群

```
/** number `Monoid` under addition */
const monoidSum: Monoid<number> = {
  concat: (x, y) => x + y,
  empty: 0
}

/** number `Monoid` under multiplication */
const monoidProduct: Monoid<number> = {
  concat: (x, y) => x * y,
  empty: 1
}

const monoidString: Monoid<string> = {
  concat: (x, y) => x + y,
  empty: ''
}

/** boolean monoid under conjunction */
const monoidAll: Monoid<boolean> = {
  concat: (x, y) => x && y,
  empty: true
}

/** boolean monoid under disjunction */
const monoidAny: Monoid<boolean> = {
  concat: (x, y) => x || y,
  empty: false
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能想知道是否所有的半群都是幺半群。事实并非如此，作为反例，考虑下面的半群

```
const semigroupSpace: Semigroup<string> = {
  concat: (x, y) => x + '  ' + y
} 
```

Enter fullscreen mode Exit fullscreen mode

我们找不到一个`empty`值使得`concat(x, empty) = x`。

让我们为更复杂的类型编写一些`Monoid`实例。我们可以为类似`Point`
的结构构建一个`Monoid`实例

```
type Point = {
  x: number
  y: number
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们能为每个字段
提供一个`Monoid`实例

```
import { getStructMonoid } from 'fp-ts/Monoid'

const monoidPoint: Monoid<Point> = getStructMonoid({
  x: monoidSum,
  y: monoidSum
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以继续用刚刚定义的实例
来填充`getStructMonoid`

```
type Vector = {
  from: Point
  to: Point
}

const monoidVector: Monoid<Vector> = getStructMonoid({
  from: monoidPoint,
  to: monoidPoint
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 折叠

当使用幺半群而不是半群时，折叠甚至更简单:我们不需要显式地提供初始值(实现可以使用幺半群的`empty`值)

```
import { fold } from 'fp-ts/Monoid'

fold(monoidSum)([1, 2, 3, 4]) // 10
fold(monoidProduct)([1, 2, 3, 4]) // 24
fold(monoidString)(['a', 'b', 'c']) // 'abc'
fold(monoidAll)([true, false, true]) // false
fold(monoidAny)([true, false, true]) // true 
```

Enter fullscreen mode Exit fullscreen mode

## 用于类型构造函数的幺半群

我们已经知道，给定`A`的半群实例，我们可以导出`Option<A>`的半群实例。

如果我们可以为`A`找到一个幺半群实例，那么我们可以为`Option<A>`(通过`getApplyMonoid`)导出一个幺半群实例，其工作方式如下

| x | y | concat(x，y) |
| --- | --- | --- |
| 没有人 | 没有人 | 没有人 |
| 一些(一) | 没有人 | 没有人 |
| 没有人 | 一些(一) | 没有人 |
| 一些(一) | 一些(b) | some(concat(a，b)) |

```
import { getApplyMonoid, some, none } from 'fp-ts/Option'

const M = getApplyMonoid(monoidSum)

M.concat(some(1), none) // none
M.concat(some(1), some(2)) // some(3)
M.concat(some(1), M.empty) // some(1) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以为`Option<A>`(对于所有的`A`)导出另外两个幺半群

1) `getFirstMonoid`...

幺半群返回最左边的非`None`值

| x | y | concat(x，y) |
| --- | --- | --- |
| 没有人 | 没有人 | 没有人 |
| 一些(一) | 没有人 | 一些(一) |
| 没有人 | 一些(一) | 一些(一) |
| 一些(一) | 一些(b) | 一些(一) |

```
import { getFirstMonoid, some, none } from 'fp-ts/Option'

const M = getFirstMonoid<number>()

M.concat(some(1), none) // some(1)
M.concat(some(1), some(2)) // some(1) 
```

Enter fullscreen mode Exit fullscreen mode

2) ...及其**对偶** : `getLastMonoid`

幺半群返回最右边的非`None`值

| x | y | concat(x，y) |
| --- | --- | --- |
| 没有人 | 没有人 | 没有人 |
| 一些(一) | 没有人 | 一些(一) |
| 没有人 | 一些(一) | 一些(一) |
| 一些(一) | 一些(b) | 一些(b) |

```
import { getLastMonoid, some, none } from 'fp-ts/Option'

const M = getLastMonoid<number>()

M.concat(some(1), none) // some(1)
M.concat(some(1), some(2)) // some(2) 
```

Enter fullscreen mode Exit fullscreen mode

例如，`getLastMonoid`可用于管理可选值

```
import { Monoid, getStructMonoid } from 'fp-ts/Monoid'
import { Option, some, none, getLastMonoid } from 'fp-ts/Option'

/** VSCode settings */
interface Settings {
  /** Controls the font family */
  fontFamily: Option<string>
  /** Controls the font size in pixels */
  fontSize: Option<number>
  /** Limit the width of the minimap to render at most a certain number of columns. */
  maxColumn: Option<number>
}

const monoidSettings: Monoid<Settings> = getStructMonoid({
  fontFamily: getLastMonoid<string>(),
  fontSize: getLastMonoid<number>(),
  maxColumn: getLastMonoid<number>()
})

const workspaceSettings: Settings = {
  fontFamily: some('Courier'),
  fontSize: none,
  maxColumn: some(80)
}

const userSettings: Settings = {
  fontFamily: some('Fira Code'),
  fontSize: some(12),
  maxColumn: none
}

/** userSettings overrides workspaceSettings */
monoidSettings.concat(workspaceSettings, userSettings)
/*
{ fontFamily: some("Fira Code"),
  fontSize: some(12),
  maxColumn: some(80) }
*/ 
```

Enter fullscreen mode Exit fullscreen mode

下一篇文章[介绍基于属性的测试](https://dev.to/gcanti/introduction-to-property-based-testing-17nk)