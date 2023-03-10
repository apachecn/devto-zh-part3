# TypeScript 中的类型安全错误处理

> 原文：<https://dev.to/_gdelgado/type-safe-error-handling-in-typescript-1p4n>

> 最初发布在我的[博客](https://gdelgado.ca/type-safe-error-handling-in-typescript.html#title)

我们以前都经历过。我们编写一个函数来处理一些边缘情况，我们使用关键字`throw`来处理这种情况:

```
type ResponseData = {
  statusCode: number
  responseBody?: ResponseBody
}

const makeHttpRequest = async (url: string): Promise<ResponseData> => {
  if (!isUrl(url)) {
    throw new Error(
      'Invalid string passed into `makeHttpRequest`. Expected a valid URL.'
    )
  }

  // ...
  // other business logic here
  // ...

  return { ... } // ResponseData
} 
```

Enter fullscreen mode Exit fullscreen mode

现在想象一个月后，你正在做你的项目，这时你或一个同事忘记将`makeHttpRequest`包装在`try / catch`块中。

这里发生了两件事:

*   编译器不再能告诉你你的代码是否不会出现运行时错误。换句话说，使用`throw`不是类型安全的。而且和`any`一样危险。它们都冲淡了使用 TypeScript 的好处。

*   因为编译器和类型都没有告诉你`makeHttpRequest`可能失败(read: throw)，你最终会得到一个运行时错误。这对每个人来说都是浪费时间、金钱和幸福。人们开始问，如果编译器不能帮助他们做一些基本的事情，比如添加一个`try / catch`块，他们为什么还要使用 TypeScript。

所以问题是:

> 我们如何将故障性编码到类型系统中？

首先，让我们首先承认`throw`不是类型安全的。为了让 TypeScript 编译器站在我们这边，我们必须使用不同的方法。

如果我们有一个代表可能失败的计算结果的`type`或`interface`会怎么样？

我们的类型将代表两个简单的结果:

*   成功案例:将返回/包含一个“成功值”(即`makeHttpRequest`情况下的`ResponseData`)
*   失败案例:返回/包含关于**为什么**失败发生的有用信息

让我们称我们的类型为直观的东西吧。让我们称我们的成功变量为`Ok`，称我们的失败变量为`Err`。

因此，如果我们将我们的类型形式化为代码，它看起来会像这样:

```
type Result<T, E>
  = Ok<T, E> // contains a success value of type T
  | Err<T, E> // contains a failure value of type E 
```

Enter fullscreen mode Exit fullscreen mode

回到我们的`makeHttpRequest`函数，我们希望将失败的可能性编码到类型系统中。

因此，`makeHttpRequest`将具有以下签名:

```
makeHttpRequest(url: string): Promise<Result<ResponseData, Error>> 
```

Enter fullscreen mode Exit fullscreen mode

函数定义应该是这样的:

```
// utility functions to build Ok and Err instances
const ok = <T, E>(value: T): Result<T, E> => new Ok(value)

const err = <T, E>(error: E): Result<T, E> => new Err(error)

const makeHttpRequest = async (url: string): Promise<Result<ResponseData, Error>> => {
  if (!isUrl(url)) {
    return err(new Error(
      'Invalid string passed into `makeHttpRequest`. Expected a valid URL.'
    ))
  }

  // ...
  // other business logic here
  // ...

  return ok({ ... }) // Ok(ResponseData)
} 
```

Enter fullscreen mode Exit fullscreen mode

当然`err(new Error('...'))`似乎有点乏味。但是有些事情你应该知道:

*   `err`函数的参数必须是`E`类型，否则在`err`内部的类型和`makeHttpRequest`的返回类型之间会出现编译错误(类型不匹配)(`E`类型被表示为`Error`实例)。

    *   相关地，为了简单起见，我选择了`Error`作为`E`的类型...意思是`E`可以是你想要的任何东西！稍后将详细介绍这一点！
*   `makeHttpRequest`的用户可以使用该功能，不用担心它会随机抛出。不再有运行时错误🚀

*   `makeHttpRequest`函数的作者也不必担心每次出现新的边缘情况时都要编写和更新文档，否则会导致函数抛出错误。函数的所有行为都编码在返回类型中。与此相关，该类型现在作为文档:“`makeHttpRequest`是一个异步函数，它可以通过`ResponseData`成功，也可以通过`Error`失败。”

..."但是，等等，我如何获得包装在`Result<T, E>`中的`T`值或`E`值？"

问得好。让我告诉你怎么做。我们将使用我制作的名为`neverthrow`的包。

`> npm install neverthrow`

```
import { ok, err, Result } from 'neverthrow'

// we'll keep this simple
type ResponseBody = {}

interface ResponseData {
  statusCode: number
  responseBody?: ResponseBody
}

const makeHttpRequest = async (
  url: string
): Promise<Result<ResponseData, Error>> => {
  if (!isUrl(url)) {
    return err(new Error(
      'Invalid string passed into `makeHttpRequest`. Expected a valid URL.'
    ))
  }

  // ...
  // other business logic here
  // ...

  return ok({ ... }) // Ok(ResponseData)
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们现在在与上一个代码片段相同的地方，除了这次我们使用了`neverthrow`包。

如果你通读一下`neverthrow` [文档](https://github.com/gdelgado14/neverthrow#top-level-api)，你会看到`Result`有一个`.map`方法，它获取`Result`中的`T`值，并将其转换成你想要的任何值。

这里有一个例子:

```
import { makeHttpRequest } from './http-api.ts'

const run = async () => {
  // unwrap the Promise
  // at this point
  // we have a Result<ResponseData, Error>
  const result = await makeHttpRequest('https://jsonplaceholder.typicode.com/todos/1')

  result.map(responseData => {
    console.log(responseData)
  })
}

run() 
```

Enter fullscreen mode Exit fullscreen mode

但是等等，如果结果变量包含一个`E`值呢？换句话说，它是一个`Err`而不是一个`Ok`。

好吧，同样的，`neverthrow`的文档也告诉你如何处理这种情况...就用`mapErr`！

```
import { makeHttpRequest } from './http-api.ts'

const run = async () => {
  // unwrap the Promise
  // at this point
  // we have a Result<ResponseData, Error>
  const result = await makeHttpRequest('https://jsonplaceholder.typicode.com/todos/1')

  result.mapErr(errorInstance => {
    console.log(errorInstance)
  })
}

run() 
```

Enter fullscreen mode Exit fullscreen mode

关于`Result` s 最美妙的事情是**它们是可链接的**！下面是上面代码中一个更现实的例子:

```
import { makeHttpRequest } from './http-api.ts'

const run = async () => {
  // unwrap the Promise
  // at this point
  // we have a Result<ResponseData, Error>
  const result = await makeHttpRequest('https://jsonplaceholder.typicode.com/todos/1')

  result
    .map(responseData => {
      // do something with the success value
    })
    .mapErr(errorInstance => {
      // do something with the failure value
    })
}

run() 
```

Enter fullscreen mode Exit fullscreen mode

使用`Result`类型可以做更多的事情(查看[文档](https://github.com/gdelgado14/neverthrow#top-level-api)，但是`map` ing 是 API 最重要的部分。

## 让你的类型更直观

如果您开始在返回类型中大量使用`Result`，您可能会注意到两件事:

*   `Result` s 的意思不是很清楚

    *   示例:数据库查询的`Result`可能类似于`Promise<Result<T, DbError>>`，而网络调用的`Result`可能类似于`Promise<Result<T, NetworkError>>`。
*   这些类型非常冗长。例如，上面我们有一个`Promise<Result<ResponseData, Error>>`...这是有点吓人的类型！

要解决这两个问题，您可以利用**类型别名**！

这里有一个例子。与其让一个函数返回一个带有`DbError`的泛型`Result`作为`E`类型，为什么不将这个类型命名为更直观的类型呢？

```
type DbResult<T> = Result<T, DbError> 
```

Enter fullscreen mode Exit fullscreen mode

现在你的函数可以直接返回`Promise<DbResult<T>>`。简洁了很多！

此外，你的类型现在编码的意义。上面的类型表明正在进行的异步可能会失败，我知道它正在处理数据库。整洁！

这是我的一个项目中的一个真实例子:

```
handler: (req: Request, res: SessionManager) => DecodeResult<Promise<RouteResult<T>>> 
```

Enter fullscreen mode Exit fullscreen mode

所以`handler`是一个做几件事的函数:

*   它对输入的数据进行解码/反序列化
*   然后它做一些异步的事情，用一些类型为`T`的数据生成一个`RouteResult`

我只通过阅读类型就知道会发生什么。美妙的是，我永远不会遇到运行时错误，因为我的代码不会抛出(我所依赖的所有第三方库都被包装以返回`Result` s)。

## 总结

*   尽可能避免使用`throw`。

    *   您的 API 的用户不需要`catch`(编译器不强制执行)。这意味着您最终会遇到运行时错误...这只是时间问题
    *   使用`throw`迫使您维护最终会变得陈旧的文档
    *   如果你设法维护你的文档，很多人不会费心通读文档，也不会意识到你的函数在某些情况下会出错
*   使用`Result` s 将失败的可能性编码到你的类型中

    *   这些类型是自文档化的，它们不会变得“陈旧”
    *   给用户一个友好的 API，让他们以安全的方式处理失败值(使用`map`和`mapErr`)
*   有一个经过全面测试和类型检查的 npm 包叫做`neverthrow`...试试吧！