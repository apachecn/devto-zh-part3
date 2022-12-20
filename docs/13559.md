# 使用 fp-ts 与非功能代码的互操作性

> 原文：<https://dev.to/gcanti/interoperability-with-non-functional-code-using-fp-ts-432e>

有时你被迫与不是用函数式风格编写的代码进行互操作，让我们看看如何处理它。

## 哨兵

用例:一个 API 可能会失败并返回一个特殊的代码域值。

示例:`Array.prototype.findIndex`

解决方案:`Option`

```
import { Option, none, some } from 'fp-ts/Option'

function findIndex<A>(
  as: Array<A>,
  predicate: (a: A) => boolean
): Option<number> {
  const index = as.findIndex(predicate)
  return index === -1 ? none : some(index)
} 
```

Enter fullscreen mode Exit fullscreen mode

## `undefined`和`null`

用例:一个可能失败并返回`undefined`(或`null`)的 API。

示例:`Array.prototype.find`

解决方案:`Option`、`fromNullable`、

```
import { Option, fromNullable } from 'fp-ts/Option'

function find<A>(as: Array<A>, predicate: (a: A) => boolean): Option<A> {
  return fromNullable(as.find(predicate))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 异常情况

用例:一个可能抛出。

示例:`JSON.parse`

解决方案:`Either`、`tryCatch`、

```
import { Either, tryCatch } from 'fp-ts/Either'

function parse(s: string): Either<Error, unknown> {
  return tryCatch(
    () => JSON.parse(s),
    (reason) => new Error(String(reason))
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

## 随机值

用例:返回不确定值的 API。

示例:`Math.random`

解决方案:`IO`

```
import { IO } from 'fp-ts/IO'

const random: IO<number> = () => Math.random() 
```

Enter fullscreen mode Exit fullscreen mode

## 同步副作用

用例:读取和/或写入全局状态的 API。

示例:`localStorage.getItem`

解决方案:`IO`

```
import { Option, fromNullable } from 'fp-ts/Option'
import { IO } from 'fp-ts/IO'

function getItem(key: string): IO<Option<string>> {
  return () => fromNullable(localStorage.getItem(key))
} 
```

Enter fullscreen mode Exit fullscreen mode

用例:读取和/或写入全局状态的 API，可能会抛出。

示例:`readFileSync`

解决方案:`IOEither`、`tryCatch`、

```
import * as fs from 'fs'
import { IOEither, tryCatch } from 'fp-ts/IOEither'

function readFileSync(path: string): IOEither<Error, string> {
  return tryCatch(
    () => fs.readFileSync(path, 'utf8'),
    (reason) => new Error(String(reason))
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

## 异步副作用

用例:执行异步计算的 API。

示例:从标准输入读取

解决方案:`Task`

```
import { createInterface } from 'readline'
import { Task } from 'fp-ts/Task'

const read: Task<string> = () =>
  new Promise<string>((resolve) => {
    const rl = createInterface({
      input: process.stdin,
      output: process.stdout
    })
    rl.question('', (answer) => {
      rl.close()
      resolve(answer)
    })
  }) 
```

Enter fullscreen mode Exit fullscreen mode

用例:执行异步计算的 API，可能会拒绝。

示例:`fetch`

解决方案:`TaskEither`、`tryCatch`、

```
import { TaskEither, tryCatch } from 'fp-ts/TaskEither'

function get(url: string): TaskEither<Error, string> {
  return tryCatch(
    () => fetch(url).then((res) => res.text()),
    (reason) => new Error(String(reason))
  )
} 
```

Enter fullscreen mode Exit fullscreen mode