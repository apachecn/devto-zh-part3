# 功能设计:代数数据类型

> 原文：<https://dev.to/gcanti/functional-design-algebraic-data-types-36kf>

构建新应用程序的第一步是定义它的领域模型。TypeScript 提供了许多工具来帮助您完成这项任务。**代数数据类型**(简称 ADT)就是这些工具之一。

# 什么是 ADT？

在计算机程序设计，尤其是函数式程序设计和类型理论中，代数数据类型是一种复合类型，即**由其他类型**组合而成的类型。

两种常见的代数类型是:

*   **产品类型**
*   **总和类型**

# 产品类型

产品类型是由集合`I`索引的类型 T <sub>i</sub> 的集合。

这个家族的两个常见成员是`n`-元组，其中`I`是自然数的非空区间...

```
type Tuple1 = [string] // I = [0]
type Tuple2 = [string, number] // I = [0, 1]
type Tuple3 = [string, number, boolean] // I = [0, 1, 2]

// Accessing by index
type Fst = Tuple2[0] // string
type Snd = Tuple2[1] // number 
```

...和结构，其中`I`是一组标签。

```
// I = {"name", "age"}
interface Person {
  name: string
  age: number
}

// Accessing by label
type Name = Person['name'] // string
type Age = Person['age'] // number 
```

## 为什么是“产品”类型？

如果我们用`C(A)`来表示`A`类型的居民数量(也就是它的**基数**，那么下面的等式成立

```
C([A, B]) = C(A) * C(B) 
```

> 乘积的基数是基数的乘积

**例题**

```
type Hour = 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12
type Period = 'AM' | 'PM'
type Clock = [Hour, Period] 
```

`Clock`类型有`12 * 2 = 24`个居民。

## 什么时候应该使用产品类型？

每当其组件**独立**时。

```
type Clock = [Hour, Period] 
```

这里`Hour`和`Period`是独立的，即`Hour`的值不影响`Period`的值，反之亦然，所有的配对都是合法且有意义的。

# 求和类型

sum 类型是一种用于保存值的数据结构，该值可以采用几种不同但固定的类型。在任一时刻，只能使用其中一种类型，并且一个标记字段会明确指示使用哪一种类型。

在 TypeScript 文档中，它们被命名为*标记的联合类型*。

**例子**(重复动作)

```
type Action =
  | {
      type: 'ADD_TODO'
      text: string
    }
  | {
      type: 'UPDATE_TODO'
      id: number
      text: string
      completed: boolean
    }
  | {
      type: 'DELETE_TODO'
      id: number
    } 
```

`type`字段是*标签*，确保其成员不相交。

## 构造函数

有`n`个成员的 sum 类型需要`n` **个构造函数**，每个成员一个

```
const add = (text: string): Action => ({
  type: 'ADD_TODO',
  text
})

const update = (id: number, text: string, completed: boolean): Action => ({
  type: 'UPDATE_TODO',
  id,
  text,
  completed
})

const del = (id: number): Action => ({
  type: 'DELETE_TODO',
  id
}) 
```

求和类型可以是**多态**和/或**递归**。

**例子**(链表)

```
//        ↓ type parameter
type List<A> = { type: 'Nil' } | { type: 'Cons'; head: A; tail: List<A> }
//                                                              ↑ recursion 
```

## 模式匹配

JavaScript 没有[模式匹配](https://github.com/tc39/proposal-pattern-matching)(因此 TypeScript)但是我们可以通过定义一个`fold`函数
来定义一个“穷人”模式匹配

```
const fold = <A, R>(
  fa: List<A>,
  onNil: () => R,
  onCons: (head: A, tail: List<A>) => R
): R => (fa.type === 'Nil' ? onNil() : onCons(fa.head, fa.tail)) 
```

**例子**(递归计算一个`List`的长度)

```
const length = <A>(fa: List<A>): number =>
  fold(fa, () => 0, (_, tail) => 1 + length(tail)) 
```

## 为什么是“总和”类型？

以下等式成立

```
C(A | B) = C(A) + C(B) 
```

> 和的基数是基数的和

**例题**(`Option`式)

```
type Option<A> =
  | { type: 'None' }
  | {
      type: 'Some'
      value: A
    } 
```

从通式`C(Option<A>) = 1 + C(A)`中，我们可以推导出`Option<boolean>` : `1 + 2 = 3`居民的基数。

## 什么时候应该使用 sum 类型？

如果实现为产品类型，其组件将依赖于。

**举例**(组件道具)

```
interface Props {
  editable: boolean
  onChange?: (text: string) => void
}

class Textbox extends React.Component<Props> {
  render() {
    if (this.props.editable) {
      // error: Cannot invoke an object which is possibly 'undefined' :(
      this.props.onChange(...)
    }
  }
} 
```

这里的问题是`Props`被建模为产品类型，但是`onChange` **依赖于**依赖于`editable`。

求和型是更好的选择

```
type Props =
  | {
      type: 'READONLY'
    }
  | {
      type: 'EDITABLE'
      onChange: (text: string) => void
    }

class Textbox extends React.Component<Props> {
  render() {
    switch (this.props.type) {
      case 'EDITABLE' :
        this.props.onChange(...) // :)
      ...
    }
  }
} 
```

**例子**(节点回调)

```
declare function readFile(
  path: string,
  //         ↓ ---------- ↓ CallbackArgs
  callback: (err?: Error, data?: string) => void
): void 
```

结果被建模为产品类型

```
type CallbackArgs = [Error | undefined, string | undefined] 
```

然而，它的组件是依赖于**的**:你要么得到一个错误**要么得到一个字符串**

| 犯罪 | 数据 | 合法？ |
| --- | --- | --- |
| `Error` | `undefined` | -好的 |
| `undefined` | `string` | -好的 |
| `Error` | `string` | ✘ |
| `undefined` | `undefined` | ✘ |

sum 类型会是更好的选择，但是是哪一种呢？

# 功能错误处理

让我们看看如何在函数式风格中处理错误。

## `Option`式

`Option`类型表示可能失败或产生`A`
类型值的计算结果

```
type Option<A> =
  | { type: 'None' } // represents a failure
  | { type: 'Some'; value: A } // represents a success 
```

构造函数和模式匹配:

```
// a nullary constructor can be implemented as a constant
const none: Option<never> = { type: 'None' }

const some = <A>(value: A): Option<A> => ({ type: 'Some', value })

const fold = <A, R>(fa: Option<A>, onNone: () => R, onSome: (a: A) => R): R =>
  fa.type === 'None' ? onNone() : onSome(fa.value) 
```

`Option`类型可以用来避免抛出异常和/或表示可选值，所以我们可以从...

```
//                this is a lie ↓
const head = <A>(as: Array<A>): A => {
  if (as.length === 0) {
    throw new Error('Empty array')
  }
  return as[0]
}

let s: string
try {
  s = String(head([]))
} catch (e) {
  s = e.message
} 
```

...在类型系统不知道可能的故障的情况下...

```
//                              ↓ the type system "knows" that this computation may fail
const head = <A>(as: Array<A>): Option<A> => {
  return as.length === 0 ? none : some(as[0])
}

const s = fold(head([]), () => 'Empty array', a => String(a)) 
```

...其中故障的可能性被*提升*到类型系统。

## `Either`式

`Either`的一个常见用途是作为`Option`的替代，用于处理可能的缺失值。在这种用法中，`None`被替换为`Left`，它可以包含有用的信息。`Right`取代`Some`。惯例规定`Left`用于失败，`Right`用于成功。

```
type Either<L, A> =
  | { type: 'Left'; left: L } // represents a failure
  | { type: 'Right'; right: A } // represents a success 
```

构造函数和模式匹配:

```
const left = <L, A>(left: L): Either<L, A> => ({ type: 'Left', left })

const right = <L, A>(right: A): Either<L, A> => ({ type: 'Right', right })

const fold = <L, A, R>(
  fa: Either<L, A>,
  onLeft: (left: L) => R,
  onRight: (right: A) => R
): R => (fa.type === 'Left' ? onLeft(fa.left) : onRight(fa.right)) 
```

回到我们的回调例子

```
declare function readFile(
  path: string,
  callback: (err?: Error, data?: string) => void
): void

readFile('./myfile', (err, data) => {
  let message: string
  if (err !== undefined) {
    message = `Error: ${err.message}`
  } else if (data !== undefined) {
    message = `Data: ${data.trim()}`
  } else {
    // should never happen
    message = 'The impossible happened'
  }
  console.log(message)
}) 
```

我们可以把它的签名改成

```
declare function readFile(
  path: string,
  callback: (result: Either<Error, string>) => void
): void 
```

然后像这样消费 API

```
readFile('./myfile', e => {
  const message = fold(e, err => `Error: ${err.message}`, data => `Data: ${data.trim()}`)
  console.log(message)
}) 
```

# 结论

在这篇文章中，我们已经看到了乘积类型和总和类型，以及根据它们所代表的状态数进行推理是如何极大地影响我们的领域模型的设计的。

许多现实世界 API 的一个常见缺陷是误用产品类型，除了所有合法状态之外，还模拟了许多非法状态。

Sum 类型是一个非常有用的基本语言特性，它们是设计优秀领域模型的关键，因为它们允许非法状态不可表示。