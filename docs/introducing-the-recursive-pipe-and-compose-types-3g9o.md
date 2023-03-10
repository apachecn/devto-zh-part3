# 介绍递归“管道”和“合成”类型

> 原文：<https://dev.to/hemaka/introducing-the-recursive-pipe-and-compose-types-3g9o>

事实证明，递归的`Pipe`(和`Compose`)类型比使用参数重载的传统方法提供了关键的优势。主要优势是:

*   保留变量名
*   函数签名中更好的泛型容差
*   变量入口函数
*   理论上可以组成无限的函数

[![](img/1b9523cf2b26b00c4117b309b4f1b128.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RT6-ZeKF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vifs20qhv6dc1o5vgknh.png)

[![](img/707546dc2c1ec22bab4ed249a1958e29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rMAXLTnP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8dtdgyhzd5i8dc4x2tw8.png)

在本文中，我们将探索这样的`Pipe`和`Compose`类型是如何构建的。

如果你想了解手头的全部资料，请看这个回购:

[https://github.com/babakness/pipe-and-compose-types](https://github.com/babakness/pipe-and-compose-types)

# 简介

我的项目之旅始于创建递归`Pipe`和`Compose`类型的挑战，不依赖于重命名参数的重载。要查看如何用重载构建它的例子，请点击这些链接中的一个，链接到优秀的库`fp-ts`，作者是 [@gcanti](https://dev.to/gcanti)

管道:

[https://github . com/gcanti/FP-ts/blob/master/src/function . ts # L222](https://github.com/gcanti/fp-ts/blob/master/src/function.ts#L222)

撰写:

[https://github . com/gcanti/FP-ts/blob/master/src/function . ts # L160](https://github.com/gcanti/fp-ts/blob/master/src/function.ts#L160)

我在自己的项目中使用过这个策略。在这种情况下，参数名称会丢失并被替换为字母名称，如`a`和`b`。

TypeScript 与 JavaScript 共享其生态系统。因为 JavaScript 缺少类型，所以参数名在指导使用方面特别有用。

让我们更近距离地看看这些类型是如何工作的。

# 制作递归管道类型

首先，我们需要一些助手类型。这些将从一个通用函数中提取信息:

```
export type ExtractFunctionArguments<Fn> = Fn extends  ( ...args: infer P ) => any  ? P : never
export type ExtractFunctionReturnValue<Fn> = Fn extends  ( ...args: any[] ) => infer P  ? P : never 
```

接下来还有两个助手，一个简单的类型允许我们根据测试类型分支不同的类型，还有一个表示任意函数的简写。

```
type BooleanSwitch<Test, T = true, F = false> = Test extends true ? T : F
export type AnyFunction = ( ...args: any[] ) => any 
```

下一种类型是非常深奥和特别的:

```
 type Arbitrary = 'It was 1554792354 seconds since Jan 01, 1970 when I wrote this' 
type IsAny<O, T = true, F = false> = Arbitrary extends O
  ? any extends O
    ? T
    : F
  : F 
```

本质上，这种类型检测`any`和`unknown`。它在`{}`上变得混乱。无论如何，它不会出口，也不会用于内部使用。

有了这些助手，下面是类型管道:

```
type Pipe<Fns extends any[], IsPipe = true, PreviousFunction = void, InitalParams extends any[] = any[], ReturnType = any> = {
  'next': ( ( ..._: Fns ) => any ) extends ( ( _: infer First, ..._1: infer Next ) => any )
    ? PreviousFunction extends void
        ? Pipe<Next, IsPipe, First, ExtractFunctionArguments<First>, ExtractFunctionReturnValue<First> >
        : ReturnType extends ExtractFunctionArguments<First>[0]
          ? Pipe<Next, IsPipe, First, InitalParams, ExtractFunctionReturnValue<First> >
          : IsAny<ReturnType> extends true
            ? Pipe<Next, IsPipe, First, InitalParams, ExtractFunctionReturnValue<First> >
            : {
              ERROR: ['Return type ', ReturnType , 'does comply with the input of', ExtractFunctionArguments<First>[0]],
              POSITION: ['Position of problem for input arguments is at', Fns['length'], 'from the', BooleanSwitch<IsPipe, 'end', 'beginning'> , 'and the output of function to the ', BooleanSwitch<IsPipe, 'left', 'right'>],
            }
    : never
  'done': ( ...args: InitalParams ) => ReturnType,
}[
  Fns extends []
    ? 'done'
    : 'next'
] 
```

[![](img/b6dfe04916d550958d718785b67edd4f.png)](https://i.giphy.com/media/vmv47p4zksWDC/giphy.gif)

这种类型经历了一系列的步骤，它从迭代每个函数开始，从头部开始，递归地将尾部传递到下一次迭代。实现这一点的关键是使用这种技术从剩余的函数中提取并分离出函数数组中的第一项:

```
( ( ..._: Fns ) => any ) extends ( ( _: infer First, ..._1: infer Next ) => any ) 
```

如果我们不做错误检查，我们可以简单地提取下一部分

```
PreviousFunction extends void
        ? Pipe<Next, IsPipe, First, ExtractFunctionArguments<First>, ExtractFunctionReturnValue<First> >
        : Pipe<Next, IsPipe, First, InitalParams, ExtractFunctionReturnValue<First> > 
```

`PreviousFunction`仅在第一次迭代时无效。在这种情况下，我们提取初始参数。我们在每次迭代中用最后一个函数返回类型传递回`InitialParams`。一旦我们穷尽了列表中的所有功能，这部分

```
 Fns extends []
    ? 'done'
    : 'next' 
```

返回`done`，我们可以返回一个由初始参数和最后一个返回类型
组成的新函数

```
'done': ( ...args: InitalParams ) => ReturnType, 
```

其他位是错误检测。如果它检测到一个错误，它将返回自定义对象，该对象将指向发生错误的计数。换句话说，它有内置的错误报告。

我是通过研究别人的库了解到这个技术的。一个显著的例子是`typescript-tuple`，我们稍后用它来构造`Compose`

好了，现在让我们为管道函数本身创建一个别名

```
type PipeFn = <Fns extends [AnyFunction, ...AnyFunction[]] >( 
  ...fns: Fns & 
    Pipe<Fns> extends AnyFunction 
      ? Fns 
      : never 
) =>  Pipe<Fns> 
```

这里是另一种技术来说明。当我们`Pipe`函数返回有用的 error 对象时，我们实际上也想引发一个编译器错误。我们通过有条件地将`fns`的匹配类型连接到自身或`never`来实现这一点。产生错误的后一种情况。

最后，我们准备定义管道。

我在不同的项目中这样做，而不仅仅是在同一个项目的不同文件中。我这样做有两个原因:

首先，我想把实现和类型分开。您可以自由使用这些类型，而不需要包含任何 JavaScript。

第二，一旦类型编译正确，我希望将未来 TypeScript 版本的优点和缺点与类型和实现分开。

# 实现管道功能

```
export const pipe: PipeFn =  ( entry: AnyFunction, ...funcs: Function1[] ) =>  ( 
  ( ...arg: unknown[] ) => funcs.reduce( 
    ( acc, item ) => item.call( item, acc ), entry( ...arg ) 
  ) 
) 
```

让我们看看它是如何工作的:

```
const average = pipe(
  ( xs: number[]) => ( [sum(xs), xs.length] ),
  ( [ total, length ] ) => total / length
) 
```

✅我们看到平均有正确的类型`(xs: number[]) => string`和参数名称被保留。

让我们试试另一个例子:

```
const intersparse = pipe( 
  ( text: string, value: string ): [string[], string] => ([ text.split(''), value ]),
  ( [chars, value]: [ string[], string ] ) => chars.join( value )
) 
```

✅保留两个参数名`(text: string, value: string) => string`

让我们尝试一个变量的例子:

```
const longerWord = ( word1: string, word2: string ) => (
  word1.length > word2.length 
    ? word1 
    : word2
)
const longestWord = ( word: string, ...words: string[]) => (
  [word,...words].reduce( longerWord, '' )
)

const length = ( xs: string | unknown[] ) => xs.length

const longestWordLength = pipe(
  longestWord,
  length,
) 
```

✅参数名称和类型检查，`longestNameLength`的类型为`(word: string, ...words: string[]) => number`

太好了！

# 作曲

事实证明，我们可以很容易地为`Compose`做到这一点。我们需要的助手将从`typescript-tuple`开始使用。

```
import { Reverse } from 'typescript-tuple'
export type Compose<Fns extends any[]> = Pipe<Reverse<Fns>, false>
export type ComposeFn = <Fns extends [AnyFunction, ...AnyFunction[]] >( 
  ...fns: Fns & 
    Compose<Fns> extends AnyFunction 
      ? Fns 
      : never 
) =>  Compose<Fns> 
```

实现只是略有不同

```
import { ComposeFn } from 'pipe-and-compose-types'
export const compose: ComposeFn = ( first: Function1, ...funcs:  AnyFunction[] ): any => {
  /* `any` is used as return type because on compile error we present an object, 
      which will not match this */
  return ( ...arg: unknown[] ) => init( [first, ...funcs] ).reduceRight( 
    (acc, item) => item.call( item, acc ), last(funcs)( ...arg ) 
  )
} 
```

让我们来测试一下新的`compose`:

```
const longestWordComposeEdition = compose(
  length,
  longestWord,
) 
```

✅参数名称和类型检查，`longestNameLength`的类型为`(word: string, ...words: string[]) => number`

# 关闭

我鼓励你看看这个回购协议，以审查类型

[https://github.com/babakness/pipe-and-compose-types](https://github.com/babakness/pipe-and-compose-types)

若要将类型导入到您自己的项目中，请使用以下命令安装:

`npm install pipe-and-compose-types`

也看看这些类型两个伟大的应用

[https://github.com/babakness/pipe-and-compose](https://github.com/babakness/pipe-and-compose)

使用将这些函数导入到您的项目中

`npm install pipe-and-compose`

请分享你的想法！也可以随时在 Twitter 上联系我！