# fp-ts 入门:非此即彼还是验证

> 原文：<https://dev.to/gcanti/getting-started-with-fp-ts-either-vs-validation-5eja>

# 问题

假设您必须实现一个 web 表单来注册一个帐户。该表单包含两个字段:`username`和`password`，并且必须满足以下验证规则:

*   `username`不能为空
*   不能有破折号
*   `password`至少需要 6 个字符
*   `password`至少需要一个大写字母
*   `password`至少需要一个数字

# 要么

`Either<E, A>`类型表示一个计算，该计算可能由于类型`E`的错误而失败，或者由于类型`A`的值而成功，因此是实现我们的验证规则的一个很好的候选。

例如，让我们对每个`password`规则
进行编码

```
import { Either, left, right } from 'fp-ts/Either'

const minLength = (s: string): Either<string, string> =>
  s.length >= 6 ? right(s) : left('at least 6 characters')

const oneCapital = (s: string): Either<string, string> =>
  /[A-Z]/g.test(s) ? right(s) : left('at least one capital letter')

const oneNumber = (s: string): Either<string, string> =>
  /[0-9]/g.test(s) ? right(s) : left('at least one number') 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用链接所有的规则...`chain`

```
import { chain } from 'fp-ts/Either'
import { pipe } from 'fp-ts/function'

const validatePassword = (s: string): Either<string, string> =>
  pipe(
    minLength(s),
    chain(oneCapital),
    chain(oneNumber)
  ) 
```

Enter fullscreen mode Exit fullscreen mode

因为我们使用了`Either`，所以检查是**快速失败**。也就是说，任何失败的检查都会使后续的检查短路，因此我们只会得到一个错误。

```
console.log(validatePassword('ab'))
// => left("at least 6 characters")

console.log(validatePassword('abcdef'))
// => left("at least one capital letter")

console.log(validatePassword('Abcdef'))
// => left("at least one number") 
```

Enter fullscreen mode Exit fullscreen mode

然而，这可能导致一个坏的 UX，最好能同时报告所有这些错误。

抽象在这里可能会有所帮助。

# 验证

验证很像`Either<E, A>`，它们代表一个计算，可能会因类型为`E`的错误而失败，或者因类型为`A`的值而成功，但是与通常涉及`Either`的计算相反，它们能够**收集多个失败**。

为了做到这一点，我们必须告诉验证如何将类型为`E`的两个值组合在一起。

这就是[半群](https://dev.to/gcanti/getting-started-with-fp-ts-semigroup-2mf7)的意义所在:组合两个同类型的值。

例如，我们可以将错误打包到一个非空数组中。

`'fp-ts/Either'`模块提供了一个`getValidation`函数，在给定一个半群的情况下，该函数为`Either`T5 返回一个替代的[适用的](https://dev.to/gcanti/getting-started-with-fp-ts-applicative-1kb3)实例

```
import { getSemigroup } from 'fp-ts/NonEmptyArray'
import { getValidation } from 'fp-ts/Either'

const applicativeValidation = getValidation(getSemigroup<string>()) 
```

Enter fullscreen mode Exit fullscreen mode

然而，为了使用`applicativeValidation`，我们必须首先重新定义所有的规则，以便它们返回一个类型为`Either<NonEmptyArray<string>, string>`的值。

让我们定义一个[组合子](https://dev.to/gcanti/functional-design-combinators-14pn)，将输出`Either<E, A>`的检查转换为输出`Either<NonEmptyArray<E>, A>`
的检查，而不是重写所有前面的函数，这很麻烦

```
import { NonEmptyArray } from 'fp-ts/NonEmptyArray'
import { mapLeft } from 'fp-ts/Either'

function lift<E, A>(check: (a: A) => Either<E, A>): (a: A) => Either<NonEmptyArray<E>, A> {
  return a =>
    pipe(
      check(a),
      mapLeft(a => [a])
    )
}

const minLengthV = lift(minLength)
const oneCapitalV = lift(oneCapital)
const oneNumberV = lift(oneNumber) 
```

Enter fullscreen mode Exit fullscreen mode

让我们把这些放在一起，我将使用`sequenceT`助手，它接受`n`动作并从左到右执行这些动作，返回结果元组

```
import { sequenceT } from 'fp-ts/Apply'
import { map } from 'fp-ts/Either'

function validatePassword(s: string): Either<NonEmptyArray<string>, string> {
  return pipe(
    sequenceT(getValidation(getSemigroup<string>()))(
      minLengthV(s),
      oneCapitalV(s),
      oneNumberV(s)
    ),
    map(() => s)
  )
}
console.log(validatePassword('ab'))
// => left(["at least 6 characters", "at least one capital letter", "at least one number"]) 
```

Enter fullscreen mode Exit fullscreen mode

# 附录

注意`sequenceT`助手能够处理不同类型的动作:

```
interface Person {
  name: string
  age: number
}

// Person constructor
const toPerson = ([name, age]: [string, number]): Person => ({
  name,
  age
})

const validateName = (s: string): Either<NonEmptyArray<string>, string> =>
  s.length === 0 ? left(['Invalid name']) : right(s)

const validateAge = (s: string): Either<NonEmptyArray<string>, number> =>
  isNaN(+s) ? left(['Invalid age']) : right(+s)

function validatePerson(name: string, age: string): Either<NonEmptyArray<string>, Person> {
  return pipe(
    sequenceT(applicativeValidation)(validateName(name), validateAge(age)),
    map(toPerson)
  )
} 
```

Enter fullscreen mode Exit fullscreen mode