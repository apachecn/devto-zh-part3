# const 断言是致命的新类型脚本特性

> 原文：<https://dev.to/bnevilleoneill/const-assertions-are-the-killer-new-typescript-feature-5d1h>

[![](img/127522983e64cf1a8734c1a09ba024fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YBUML21---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aui0RIOYORyfWoOlbSDSFGA.jpeg)

Typescript 3.4 已经发布，虽然我发现官方的 Typescript 文档信息量很大，但它们总感觉有点太学术性，有点枯燥。当我发现新功能时，我想知道该功能将治愈的确切疾病。

在我看来，const 断言是 TypeScript 3.4 的杀手级新特性，正如我将在后面解释的那样，使用这个新特性，我可以省略许多繁琐的类型声明。

### 常量断言

```
const x = { text: "hello" } as const; 
```

Enter fullscreen mode Exit fullscreen mode

[官方文件](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html)给出这样的解释:

> TypeScript 3.4 为文字值引入了一个新的构造，称为 const 断言。它的语法是用 const 代替类型名的类型断言(例如 123 作为 const)。当我们用常量断言构造新的文字表达式时，我们可以向语言发出信号
> 
> 该表达式中的任何文字类型都不应被加宽(例如，不能从“hello”到 string)
> 
> 对象文字获取`readonly`属性
> 
> 数组文字变成了`readonly`元组

这感觉有点干涩，有点混乱。让我们一次分解一个要点。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 文字类型没有类型加宽

并不是每个人都知道什么是类型加宽，由于一些意想不到的行为，当它第一次被发现时，人们感到非常惊讶。

当我们使用关键字 const 声明一个文本变量时，类型就是等号右边的文本，例如:

`const x = 'x'; // x has the type 'x'`

const 关键字确保不会发生对变量的重新赋值，并且只保证该文字的严格类型。

但是如果我们使用 let 而不是 const，那么我们就把这个变量留给了一个重新赋值的机会，并且这个类型被扩展为字面量的类型，就像这样:

```
let x = 'x'; // x has the type string; 
```

Enter fullscreen mode Exit fullscreen mode

下面是两个不同的声明:

```
const x = 'x'; // has the type 'x' 
let y = 'x'; // has the type string 
```

Enter fullscreen mode Exit fullscreen mode

y 被扩展为更一般的类型，这将允许它被重新分配给该类型的其他值，并且 x 只能具有值“x”。

有了新的常量特性，我可以这样做:

```
let y = 'x' as const; // y has type 'x' 
```

Enter fullscreen mode Exit fullscreen mode

如果我这样做，而不是简单地将 y 声明为一个常量变量，那么在任何好的代码评审中，我都会被赶出去，但是让我们快速地转到文档中的第二点:

### 对象文字获取只读属性

在 Typescript 3.4 之前，使用对象文本进行了全面的类型扩展:

```
const action = { type: 'INCREMENT', } // has type { type: string } 
```

Enter fullscreen mode Exit fullscreen mode

即使我们已经将 action 声明为一个 const，type 属性仍然可以被重新分配，因此，该属性被扩展为 string 类型。

这仍然不觉得有用，所以让我们用一个更好的例子。

如果您熟悉 [Redux](https://redux.js.org/) ，那么您可能会认识到上面的动作变量可以用作 Redux 动作。对于那些不知道的人来说，Redux 是一个全局不可变的状态存储。通过向所谓的 reducers 发送动作来修改状态。Reducers 是纯粹的函数，它在每个动作被调度后返回全局状态的新的更新版本，反映了动作中指定的修改。

在 Redux 中，标准做法是从名为 [action creators](https://read.reduxbook.com/markdown/part1/04-action-creators.html) 的函数中创建您的动作。动作创建器是简单的纯函数，它返回 Redux 动作对象文字以及提供给函数的任何参数。

这可以用一个例子来更好地说明。一个应用程序可能需要一个全局 count 属性，为了更新这个 count 属性，我们可以调度 SET_COUNT 类型的操作，只需将全局 COUNT 属性设置为一个新值，该值是一个文字对象属性。该动作的动作创建者将是一个函数，该函数将一个数字作为参数，并返回一个具有 SET_COUNT 类型属性和 number 类型有效负载属性的对象，该有效负载属性将指定 COUNT 的新值:

```
const setCount = (n: number) => {
  return {
    type: 'SET_COUNT',
    payload: n,
  }
}

const action = setCount(3)
// action has type
// { type: string, payload: number } 
```

Enter fullscreen mode Exit fullscreen mode

从上面显示的代码可以看出，type 属性已经被拓宽为 string，而不是 SET_COUNT。这不是非常类型安全的，我们所能保证的是 type 属性是一个字符串。redux 中的每个动作都有一个 atype 属性，是一个字符串。

这不是很好，如果我们想利用类型属性上的[区别联合](https://basarat.gitbooks.io/typescript/docs/types/discriminated-unions.html)，那么在 TypeScript 3.4 之前，我们需要为每个动作声明一个接口或类型:

```
interface SetCount {
  type: 'SET_COUNT';
  payload: number;
}

const setCount = (n: number): SetCount => {
  return {
    type: 'SET_COUNT',
    payload: n,
  }
}

const action = setCount(3)
// action has type SetCount 
```

Enter fullscreen mode Exit fullscreen mode

这确实增加了编写 Redux 动作和 Redux 的负担，但是我们可以通过添加一个 const 断言来解决这个问题:

```
const setCount = (n: number) => {
  return <const>{
    type: 'SET_COUNT',
    payload: n
  }
}

const action = setCount(3);
// action has type
//  { readonly type: "SET_COUNT"; readonly payload: number }; 
```

Enter fullscreen mode Exit fullscreen mode

观察力敏锐的人会注意到，从 setCount 推断出的类型在每个属性后都附加了 readonly 修饰符，如文档中的要点所述。

事实正是如此:

```
{
  readonly type: "SET_COUNT";
  readonly payload: number
}; 
```

Enter fullscreen mode Exit fullscreen mode

动作中的每个文字都添加了 readonly 修饰符。

在 redux 中，我们建立了一个允许动作的联合，reducer 函数可以采取该联合来获得良好的类型安全，我们将围绕该联合调度动作。在 TypeScript 3.4 之前，我们会这样做:

```
interface SetCount {
  type: 'SET_COUNT';
  payload: number;
}

interface ResetCount {
  type: 'RESET_COUNT';
}

const setCount = (n: number): SetCount => {
  return {
    type: 'SET_COUNT',
    payload: n,
  }
}

const resetCount = (): ResetCount => {
  return {
    type: 'RESET_COUNT',
  }
}

type CountActions = SetCount | ResetCount 
```

Enter fullscreen mode Exit fullscreen mode

我们已经创建了两个接口 RESET_COUNT 和 SET_COUNT 来对两个动作创建者 resetCount 和 setCount 的返回类型进行类型化。

CountActions 是这两个接口的联合。

有了 const 断言，我们可以通过使用 const、 [ReturnType](https://dev.to/busypeoples/notes-on-typescript-returntype-3m5a) 和 typeof:
的组合来消除声明所有这些接口的需要

```
const setCount = (n: number) => {
  return <const>{
    type: 'SET_COUNT',
    payload: n
  }
}

const resetCount = () => {
  return <const>{
    type: 'RESET_COUNT'
  }
}

type CountActions = ReturnType<typeof setCount> | ReturnType<typeof resetCount>; 
```

Enter fullscreen mode Exit fullscreen mode

我们从动作创建者函数 setCount 和 resetCount 的返回类型中推断出了一个很好的动作联合。

### 数组文字变成`readonly`元组

在 TypeScript 3.4 之前，声明文字数组将被加宽，并对修改开放。

使用 const，我们可以将文字锁定为它们的显式值，也不允许修改。

如果我们有一个 redux 动作类型来设置一个小时数组，它可能看起来像这样:

```
const action = {
  type: 'SET_HOURS',
  payload: [8, 12, 5, 8],
}
//  { type: string; payload: number[]; }

action.payload.push(12) // no error 
```

Enter fullscreen mode Exit fullscreen mode

在 TypeScript 3.4 之前，加宽使上述操作的文字属性更加通用，因为它们可以修改。

如果我们将 const 应用于对象文字，那么我们就很好地收紧了一切:

```
>const action = <const>{
  type: 'SET_HOURS',
  payload: [8, 12, 5, 8]
}

// {
//  readonly type: "SET_HOURS";
//  readonly payload: readonly [8, 12, 5, 8];
// }

action.payload.push(12);  // error - Property 'push' does not exist on type 'readonly [8, 12, 5, 8]'. 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的事情正是文件中的要点所陈述的:

有效载荷数字数组实际上是一个由 8，12，5，8 组成的只读数组。

### 结论

我真的可以用这个代码示例来总结以上所有内容:

```
let obj = {
  x: 10,
  y: [20, 30],
  z: {
    a:
      {  b: 42 }
  } 
} as const; 
```

Enter fullscreen mode Exit fullscreen mode

对应于:

```
let obj: {
  readonly x: 10;
  readonly y: readonly [20, 30];
  readonly z: {
    readonly a: {
      readonly b: 42;
    };
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我可以推断类型，而不是编写多余的样板类型。这对于 redux 特别有用，在 redux 中，有区别的联合用于缩小对象文字表达式的类型。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

<figure>[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption></figcaption>

</figure>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

post [const assertions 是杀手级的新类型脚本特性](https://blog.logrocket.com/const-assertions-are-the-killer-new-typescript-feature-b73451f35802/)首先出现在[的 LogRocket 博客](https://blog.logrocket.com)上。