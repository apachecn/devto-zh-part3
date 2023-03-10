# 关于 TypeScript 的注释:ReturnType

> 原文：<https://dev.to/busypeoples/notes-on-typescript-returntype-3m5a>

### 简介

这些注释应该有助于更好地理解`TypeScript`，并且在需要查找在特定情况下如何利用 TypeScript 时可能会有帮助。所有示例都基于 TypeScript 3.2。

### ReturnType

在下面我们将谈论`ReturnType`。
为了更好地理解`ReturnType`，我们将一路构建示例，展示使用这种条件类型可能有用的地方。

在我们深入探讨之前，让我们写一个基本的例子来演示`ReturnType`是如何工作的。
我们有一个`getInt`函数，它期待一个`string`类型的输入，并解析给定输入的整数值。

```
function getInt(a: string) {
  return parseInt(a);
}

type A = ReturnType<typeof getInt>; // => number 
```

Enter fullscreen mode Exit fullscreen mode

使用`typeof`，我们能够获得`getInt`的类型签名，在这种情况下，这意味着`(a: string) => number`。`ReturnType`接受一个函数并返回该函数被调用时将返回的返回类型。`number`在我们的`getInt`例子中。

采用这种方法有一个主要优点，我们不需要让返回类型与函数定义保持同步。

### 高级

既然我们已经看到了`ReturnType`的作用，以及我们如何在它最基本的形式中利用这种类型，那么让我们试着更深入地理解并看看更高级的案例。

```
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any; 
```

Enter fullscreen mode Exit fullscreen mode

以上，是`ReturnType`的实现。它试图推断返回值并返回推断的类型或`any`。

在我们的下一个例子中，我们希望获得创建`User`的函数的返回类型。

```
let id = 0;
function createUser(name: string, position: string) {
  return {
    id: id++,
    name,
    position,
    createdAt: new Date()
  };
}

type User = ReturnType<typeof createUser>;
// => {id: number, name: string, position: string, createdAt: Date} 
```

Enter fullscreen mode Exit fullscreen mode

同样，`ReturnTypes`可以返回创建的`User`类型。这意味着我们不必让类型与函数声明保持同步。这里有更多使用`ReturnType`的例子。

```
type A = ReturnType<any>; // => any
type B = ReturnType<never>; // => never
type C = ReturnType<() => string>; //=> string 
```

Enter fullscreen mode Exit fullscreen mode

我们的下一个例子是试图推断出`None`类型，这里我们为`None`定义了一个常数。

```
const None = "None";

function none() {
  return {type: None}
}

type NoneType = ReturnType<typeof none>; // => {type: string} 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，类型`NoneType`被定义为`{type: string}`，但实际上应该是`{type: "None"}`。
为了使 TypeScript 能够推断出正确的类型，我们需要明确这种情况下的`None`常量。

```
const None = "None";

function none() {
  return {type: None as typeof None}
}

type NoneType = ReturnType<typeof none>; // => {type: "None"} 
```

Enter fullscreen mode Exit fullscreen mode

通过显式定义`None`类型，`ReturnType`可以推断出正确的类型。
在这些具体案例中，我们需要牢记这一点。

最后，让我们看看如何推断一个泛型函数的返回类型。

```
function identity<T>(a: T) : T {
  return a;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们来看看`ReturnType`能推断出什么:

```
type IdentityType = ReturnType<typeof identity>; // => {}
type IdentityType = ReturnType<typeof identity<number>>; // => syntax error! 
```

Enter fullscreen mode Exit fullscreen mode

所以无法推断身份。那么我们如何推断一个泛型函数的返回类型呢？

我们需要实现我们自己的返回类型，如这里建议的。

```
interface Callable<ReturnType> {
  (...args: any[]): ReturnType;
}

type GenericReturnType<ReturnType, F> = F extends Callable<ReturnType>
  ? ReturnType
  : never; 
```

Enter fullscreen mode Exit fullscreen mode

现在使用我们的`GenericReturnType`我们可以推断出正确的返回类型:

```
type IdentityType = GenericReturnType<string, typeof identity>; // => string
type IdentityType = GenericReturnType<number, typeof identity>; // => number 
```

Enter fullscreen mode Exit fullscreen mode

当谈到一般函数时，我们目前可能正在处理次优条件。上面的`GenericReturnType`将与*标识*函数一起工作，但是在处理更复杂的通用函数时可能会中断。

另外，上面的`GenericReturnType`之所以有效，是因为 TypeScript 中的接口可以描述函数类型。看看下面这个例子:

```
interface GetInt {
    (a: string): number;
}

const getInt: GetInt = (a) => parseInt(a);

type A = ReturnType<typeof getInt>; // => number 
```

Enter fullscreen mode Exit fullscreen mode

我们应该更好地理解`ReturnType`现在是如何工作的，以及在我们的应用程序中使用它时我们可以在哪里获得一些好处。

如果您有任何问题或反馈，请在此留言或通过 Twitter 联系: [A. Sharif](https://twitter.com/sharifsbeat)