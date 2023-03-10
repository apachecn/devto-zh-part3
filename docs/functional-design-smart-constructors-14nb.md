# 功能设计:智能构造器

> 原文：<https://dev.to/gcanti/functional-design-smart-constructors-14nb>

有时你需要保证程序中的值，这超出了通常的类型系统检查所能完成的范围。智能构造函数可用于此目的。

## 问题

```
interface Person {
  name: string
  age: number
}

function person(name: string, age: number): Person {
  return { name, age }
}

const p = person('', -1.2) // no error 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，`string`和`number`是宽泛的类型。如何定义一个非空字符串？还是正数？还是整数？还是正整数？

更一般地说:

> 我如何定义一个类型为`T`的**精化**？

## 菜谱

1.  定义一个代表精化的类型`R`
2.  难道**没有**为`R`导出一个构造函数
3.  用下面的签名导出一个函数(**智能构造函数**)

```
make: (t: T) => Option<R> 
```

Enter fullscreen mode Exit fullscreen mode

## 一种可能的实现:品牌类型

**品牌类型**是与*独特的*品牌
相交的类型`T`

```
type BrandedT = T & Brand 
```

Enter fullscreen mode Exit fullscreen mode

让我们按照上面的配方实现`NonEmptyString`:

1.  定义一个代表细化的类型`NonEmptyString`

```
export interface NonEmptyStringBrand {
  readonly NonEmptyString: unique symbol // ensures uniqueness across modules / packages
}

export type NonEmptyString = string & NonEmptyStringBrand 
```

Enter fullscreen mode Exit fullscreen mode

1.  难道**没有**为`NonEmptyString`导出一个构造函数

```
// DON'T do this
export function nonEmptyString(s: string): NonEmptyString { ... } 
```

Enter fullscreen mode Exit fullscreen mode

1.  一定要导出智能构造函数`make: (s: string) => Option<NonEmptyString>`

```
import { Option, none, some } from 'fp-ts/Option'

// runtime check implemented as a custom type guard
function isNonEmptyString(s: string): s is NonEmptyString {
  return s.length > 0
}

export function makeNonEmptyString(s: string): Option<NonEmptyString> {
  return isNonEmptyString(s) ? some(s) : none
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们对`age`字段
做同样的事情

```
export interface IntBrand {
  readonly Int: unique symbol
}

export type Int = number & IntBrand

function isInt(n: number): n is Int {
  return Number.isInteger(n) && n >= 0
}

export function makeInt(n: number): Option<Int> {
  return isInt(n) ? some(n) : none
} 
```

Enter fullscreen mode Exit fullscreen mode

用法

```
interface Person {
  name: NonEmptyString
  age: Int
}

function person(name: NonEmptyString, age: Int): Person {
  return { name, age }
}

person('', -1.2) // static error

const goodName = makeNonEmptyString('Giulio')
const badName = makeNonEmptyString('')
const goodAge = makeInt(45)
const badAge = makeInt(-1.2)

import { option } from 'fp-ts/Option'

option.chain(goodName, name => option.map(goodAge, age => person(name, age))) // some({ "name": "Giulio", "age": 45 })

option.chain(badName, name => option.map(goodAge, age => person(name, age))) // none

option.chain(goodName, name => option.map(badAge, age => person(name, age))) // none 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这似乎只是将运行时检查的负担推给了调用者。这是公平的，但是调用者反过来可能会把这个负担推给调用者，以此类推，直到你到达系统边界，在那里你*无论如何都应该*进行输入验证。

对于一个使在系统边界进行运行时验证变得容易并支持品牌类型的库，请查看 [io-ts](https://github.com/gcanti/io-ts)