# 迁移到 TypeScript，第 2 部分:信任编译器！

> 原文：<https://dev.to/resir014/migrating-to-typescript-part-2-trust-the-compiler-4o87>

*头球图片由[伊琳娜·伊里斯](https://unsplash.com/photos/nYIQYg8cQVc)在 [Unsplash](https://unsplash.com/) 拍摄。*

在第 1 部分中，我们探讨了如何用 TypeScript 编译器和新的 TypeScript Babel 预置初始化一个项目。在这一部分，我们将快速浏览一下 TypeScript 的特性以及它们的用途。我们还将学习如何使用现有项目中的实际代码片段，将现有的 JavaScript 项目逐步迁移到 TypeScript。这将让你学会如何一路信任编译器。

* * *

## 在打字稿中思考

TypeScript 中的静态类型和类型安全的想法可能会因为来自动态类型背景而让人感到难以接受，但事情并不一定是这样。

人们经常告诉你的关于 TypeScript 的主要事情是，它“只是带有类型的 JavaScript”。因为 JavaScript 是动态类型化的，所以很多特性，比如类型强制，经常被滥用来利用语言的动态特性。所以类型安全的概念可能永远不会出现在普通 JS 开发人员的脑海中。这使得静态类型化和类型安全的想法让人觉得势不可挡，但事情并不一定如此。

诀窍是在我们进行的过程中重组我们的思维。要做到这一点，我们需要有一个心态。正如在 [Basarat 的书](https://basarat.gitbooks.io/typescript/docs/javascript/recap.html)中所定义的，主要的心态是**你的 JavaScript 已经是打字稿了**。

### 但是为什么 TypeScript 很重要呢？

一个更合适的问题应该是**“为什么 JavaScript 中的静态类型很重要？”**迟早，你会开始用 JavaScript 编写大中型应用程序。当您的代码库变得更大时，检测 bug 将成为一项更乏味的任务。尤其是当它是那些讨厌的错误之一时。JavaScript 本质上是一种动态类型语言，它有很多奇怪的地方，比如`null`和`undefined`类型、类型强制等等。迟早，这些小怪癖会对你不利。

静态类型可以确保代码的正确性，以便尽早发现错误。像 TypeScript 和 [Flow](https://flow.org/) 这样的静态类型检查器通过在编译时检测类型错误来帮助减少代码中的错误数量。一般来说，在 JavaScript 代码中使用静态类型[可以帮助防止提交代码中大约 15%](https://blog.acolyer.org/2017/09/19/to-type-or-not-to-type-quantifying-detectable-bugs-in-javascript/) 的错误。

TypeScript 还提供了各种生产率增强，如下所示。你可以在具有一流类型脚本支持的编辑器上看到这些特性，比如 [Visual Studio Code](https://code.visualstudio.com/) 。

*   通过智能感知实现高级语句完成
*   更智能的代码重构
*   从用法推断类型的能力
*   能够对 JavaScript 文件进行类型检查(并从 JSDoc 注释中推断类型)

* * *

## 严格模式

TypeScript 的“严格模式”是整个 TypeScript 生态系统的核心。在 [TypeScript 2.3](https://blog.mariusschulz.com/2017/06/09/typescript-2-3-the-strict-compiler-option) 中引入的`--strict`编译器标志激活 TypeScript 的严格模式。默认情况下，这会将所有严格类型检查选项设置为 true，包括:

*   `--noImplicitAny` -在隐含“any”类型的表达式和声明中引发错误。
*   `--noImplicitThis` -使用隐含的“any”类型在“this”表达式上引发错误。
*   `--alwaysStrict` -在严格模式下解析，并为每个源文件发出“use strict”。
*   `--strictBindCallApply` -对函数启用严格的“绑定”、“调用”和“应用”方法。
*   `--strictNullChecks` -启用[严格的空值检查](https://basarat.gitbooks.io/typescript/docs/options/strictNullChecks.html)。
*   `--strictFunctionTypes` -启用函数类型的严格检查。
*   `--strictPropertyInitialization` -在类中启用属性初始化的严格检查。

当`tsconfig.json`中的`strict`设置为`true`时，以上所有选项都设置为`true`。如果这些选项中的一些给您带来了问题，您可以通过逐个覆盖上面的选项来覆盖严格模式。比如:

```
{  "compilerOptions":  {  "strict":  true,  "strictFunctionTypes":  false,  "strictPropertyInitialization":  false  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这将启用所有严格的类型检查选项*，除了* `--strictFunctionTypes`和`--strictPropertyInitialization`。当这些选项给你带来麻烦时，你可以随意摆弄它们。一旦你对它们更熟悉了，慢慢地一个一个地重新启用它们。

## 林挺

林挺和静态分析工具是任何语言的许多基本工具之一。对于 TypeScript 项目，目前有两种流行的林挺解决方案。

*   **[TSLint](https://palantir.github.io/tslint/)** 曾经是林挺打字稿代码事实上的工具。这些年来，它为 TS 社区提供了很好的服务，但是最近却失宠了。最近开发似乎停滞不前，作者甚至[最近宣布弃用](https://medium.com/palantir/tslint-in-2019-1a144c2317a9)ESLint。甚至微软自己也注意到了最近 TSLint 中的一些架构和性能问题，并建议不要这么做。这让我想到了下一个选择。
*   ——是，我知道。但是请听我说完。尽管在相当长的一段时间里，ESLint 仅仅是林挺 JavaScript 的一个工具，但它已经添加了越来越多的特性来更好地支持 TS。它已经宣布了通过新的 [typescript-eslint](https://github.com/typescript-eslint/typescript-eslint) 项目[更好地支持 TS](https://eslint.org/blog/2019/01/future-typescript-eslint) 的计划。它包含一个 ESLint 的 TypeScript 解析器，甚至还有一个插件[将许多 TSLint 规则移植到 ESLint](https://github.com/typescript-eslint/typescript-eslint/tree/master/packages/eslint-plugin#supported-rules) 中。

因此，ESLint 可能是更好的选择。要了解更多关于使用 ESLint for TypeScript 的信息，请通读 [typescript-eslint](https://github.com/typescript-eslint/typescript-eslint) 项目的文档。

## 打字稿类型快速入门

下一节包含一些关于 TypeScript 类型系统如何工作的快速参考。要获得更详细的指南，请阅读这篇关于 TypeScript 的类型系统的 [2ality 博客文章](http://2ality.com/2018/04/type-notation-typescript.html)。

### 应用类型

一旦你把你的`.js`文件重命名为`.ts`(或者`.tsx`)，你就可以输入类型注释。类型注释是使用`: TypeName`语法编写的。

```
let assignedNumber: number | undefined = undefined

assignedNumber = 0

function greetPerson(name: string) {
  return `Hello, ${name}!`
} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以为函数定义返回类型。

```
function isFinishedGreeting(name: string): boolean {
  return getPerson(name).isGreeted()
} 
```

Enter fullscreen mode Exit fullscreen mode

### 本原&单位类型

TypeScript 有一些受支持的基本类型。这些是 JavaScript 语言中最基本的数据类型，在某种程度上也是 TypeScript。

```
// Boolean
let isDone: boolean = false

// Number
let decimal: number = 6
let hex: number = 0xf00d
let binary: number = 0b1010
let octal: number = 0o744

// string
let standardString: string = 'Hello, world!'
let templateString: string = `Your number is ${decimal}` 
```

Enter fullscreen mode Exit fullscreen mode

这些原语类型也可以变成**单元类型**，其中的值可以是它们自己的类型。

```
// This variable can only have one possible value: 42.
let fortyTwo: 42 = 42

// A unit type can also be combined with other types.
// The `|` turns this into a union type. We'll go through it in the next section.
let maybeFalsey: 0 | false | null | undefined 
```

Enter fullscreen mode Exit fullscreen mode

### 交集&联盟类型

您可以使用交集和并集类型将两种或多种类型组合在一起。

联合类型可用于具有几种类型之一的类型/变量。这告诉 TypeScript】"变量/类型 X 可以是类型 A 也可以是类型 b。"

```
function formatCommandline(command: string[] | string) {
  var line = ''
  if (typeof command === 'string') {
    line = command.trim()
  } else {
    line = command.join('  ').trim()
  }

  return line
} 
```

Enter fullscreen mode Exit fullscreen mode

交叉点类型可用于将多种类型组合成一种类型。这告诉 TypeScript】“变量/类型 X 包含类型 A 和 b”.

```
type A = { a: string }
type B = { b: string }

type Combined = A & B // { a: string, b: string }

// Example usage of intersection types.
// Here we take two objects, then combining them into one whilst using intersection types
// to combine the types of both objects into one.
function extend<T, U>(first: T, second: U): T & U {
  // use TypeScript type casting to create an object with the combined type.
  let result = {} as T & U

  // combine the object.
  for (let id in first) {
    result[id] = first[id]
  }
  for (let id in second) {
    if (!result.hasOwnProperty(id)) {
      result[id] = second[id]
    }
  }
  return result
}

const x = extend({ a: 'hello' }, { b: 42 })

// `x` now has both `a` and `b` property
console.log(x.a)
console.log(x.b) 
```

Enter fullscreen mode Exit fullscreen mode

### `type` s 和`interface` s

要定义具有复杂结构的对象类型，可以使用`type`或`interface`语法。两者的工作本质上是一样的，`interface`非常适合带有类的面向对象模式。

```
// Types
type ComponentProps = {
  title?: string
}

function ReactComponent(props: ComponentProps) {
  return <div>{props.title}</div>
}

// Interfaces
interface TaskImpl {
  start(): void
  end(): void
}

class CreepTask implements TaskImpl {
  state: number = 0

  start() {
    this.state = 1
  }

  end() {
    this.state = 0
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 仿制药

泛型在成员之间提供了有意义的类型约束。

在下面的例子中，我们定义了一个动作类型，其中`type`属性可以是我们传递给泛型的任何内容。

```
interface Action<T = any> {
  type: T
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在泛型中定义的类型将被传递给`type`属性。在下面的例子中，`type`将拥有一个单位类型`'FETCH_USERS'`。

```
// You can also use `Action<string>` for any string value.
interface FetchUsersAction extends Action<'FETCH_USERS'> {
  payload: UserInfo[]
}

type AddUserAction = Action<'ADD_USER'>
const action: AddUserAction = { type: 'ADD_USER' } 
```

Enter fullscreen mode Exit fullscreen mode

### 申报文件

您可以让 TypeScript 知道您正在尝试描述存在于库中某处的某个代码(模块、全局变量/接口或运行时环境，如 Node)。为此，我们使用了`declare`关键字。

声明文件总是有一个`.d.ts`文件扩展名。

```
// For example, to annotate Node's `require()` call
declare const require: (module: string) => any

// Now you can use `require()` everywhere in your code!
require('whatwg-fetch') 
```

Enter fullscreen mode Exit fullscreen mode

您可以在代码中的任何地方包含它，但通常它们包含在声明文件中。声明文件有一个`.d.ts`扩展名，用于声明您自己的代码或来自其他库的代码的类型。通常，项目会将它们的声明文件包含在类似于`declarations.d.ts`的文件中，并且不会在您编译的代码中发出。

您还可以在`declare module`语法中将声明约束到某个模块。例如，这里有一个模块，它有一个名为`doSomething()`的默认导出。

```
declare module 'module-name' {
  // You can also export types inside modules so library consumers can use them.
  export type ExportedType = { a: string; b: string }

  const doSomething: (param: ExportedType) => any
  export default doSomething
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 让我们迁徙吧！

好了，别再说教了，让我们开始吧！我们将看一看一个真实的项目，取几个模块，并将它们转换成 TypeScript。

为此，我接受了我的泰国朋友的帮助，他叫 [Thai](https://dt.in.th/) (是的，我知道)。他有一个名为 [Bemuse](https://bemuse.ninja) 的大型基于网络的节奏游戏项目，他一直计划将其迁移到 TypeScript。因此，让我们看看代码的一些部分，并尝试在可能的情况下将它们迁移到 ts。

### 从`.js`到`.ts`

考虑以下模块:

[![1-non-js-file](img/86e92787b46c61511059b932548b7ede.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0-CMYAei--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bi681uy6marceq0zrmrr.png)

这里我们有典型的 JavaScript 模块。一个简单的模块，带有一个用 JSDoc 进行了类型注释的函数，以及另外两个没有注释的函数。我们要把这个坏男孩变成打字稿。

要使项目中的文件成为 TypeScript 文件，我们只需将其从`.js`重命名为`.ts`。很简单，对吧？

[![2-rename-to-ts](img/45df1173f78b25c09e36d458d4ead929.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6V-QEF3s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mug06higec3p1pa63qot.png)

哦不！我们开始看到红色了！我们做错了什么？

实际上，这很好！通过这样做，我们已经启用了 TypeScript 类型检查，所以剩下的工作就是添加我们认为合适的类型。

首先要做的是向这些函数添加参数类型。作为一种快速入门的方法，TypeScript 允许我们从用法中推断类型，并将它们包含在我们的代码中。如果您使用 Visual Studio 代码，单击当光标位于函数名称上时出现的灯泡，然后单击“从用法推断参数类型”。

[![infer-types-from-usage](img/8cdea6e95af056498449b10021221e90.png)](https://i.giphy.com/media/jy8Ii9UdsRGZxQETgq/giphy.gif)

如果使用 [JSDoc](http://usejsdoc.org/) 来记录函数/变量，这将变得容易得多，因为 ts 也可以从 JSDoc 注释中推断参数类型。

[![infer-types-from-jsdoc](img/2223ffde056edfb73d66ef71d7435272.png)](https://i.giphy.com/media/fMzOUcjYX6k8GdC8a3/giphy.gif)

请注意，TypeScript 根据使用情况在该文件的底部为函数生成了一个部分对象架构。我们可以以此为起点，使用`interface` s 和`type` s 来改进它的定义，例如，让我们看一下这一行。

```
/**
 * Returns the accuracy number for a play record.
 */
export function formattedAccuracyForRecord(record: { count: any; total: any }) {
  return formatAccuracy(calculateAccuracy(record.count, record.total))
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经知道在这个参数中有属性`count`和`total`。为了让这段代码更清晰，我们可以把这个声明放到一个单独的`type` / `interface`中。您可以将它包含在同一个文件中，或者单独包含在一个为通用类型/接口保留的文件中，例如`types.ts`

```
export type RecordItem = {
  count: any
  total: any
  [key: string]: any
}

import { RecordItem } from 'path/to/types'

/**
 * Returns the accuracy number for a play record.
 */
export function formattedAccuracyForRecord(record: RecordItem) {
  return formatAccuracy(calculateAccuracy(record.count, record.total))
} 
```

Enter fullscreen mode Exit fullscreen mode

### 处理外部模块

解决了这个问题，现在我们来看看如何用外部模块迁移文件。举个简单的例子，我们有以下模块:

[![4-raw-ts-with-modules](img/d3ba1662cb35652b37cb2169c4c9fcd7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oihrnIwG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yb3fpxlnj4yv9ph7ta3v.png)

我们刚刚将这个原始 JS 文件重命名为`.ts`，我们看到了一些错误。让我们来看看它们。

在第一行，我们可以看到 TypeScript 不理解如何处理我们导入的`lodash`模块。如果我们悬停在红色曲线上，我们可以看到以下内容:

```
Could not find a declaration file for module 'lodash-es'. '/Users/resir014/etc/repos/bemusic/bemuse/node_modules/lodash/lodash.js' implicitly has an 'any' type.
  Try `npm install @types/lodash` if it exists or add a new declaration (.d.ts) file containing `declare module 'lodash';` 
```

Enter fullscreen mode Exit fullscreen mode

正如错误消息所说，我们需要做的就是安装`lodash`的类型声明来修复这个错误。

```
$ npm install --save-dev @types/lodash 
```

Enter fullscreen mode Exit fullscreen mode

这个声明文件来自于 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) ，一个广泛的库社区维护的节点运行时声明文件，以及许多流行的库。所有这些都是在 npm 上的`@types/`范围内自动生成和发布的。

有些库包含自己的声明文件。如果项目是从 TypeScript 编译的，将自动生成声明。您还可以为自己的库手动创建声明文件，即使您的项目不是使用 TypeScript 生成的。在模块内部生成声明文件时，一定要将它们包含在`package.json`中的`types`或`typings`键中。这将确保 TypeScript 编译器知道在哪里寻找所述模块的声明文件。

```
{  "main":  "./lib/index.js",  "types":  "./types/index.d.ts"  } 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们已经安装了类型声明，我们的 TS 文件看起来怎么样？

[![5-installed-declarations](img/33fdabd19af887fedb261384084f937d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0pe_ZNn4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ui0btlep4ymrk8tls75k.png)

哇，这是什么？我以为只有一个错误会消失？这里发生了什么事？

TypeScript 的另一个强大之处在于，它能够根据数据在模块中的流动方式来推断类型。这被称为*基于控制流的类型分析*。这意味着 TypeScript 将知道`.orderBy()`调用中的`chart`来自之前调用传递的内容。因此，我们现在必须修复的唯一类型错误是函数参数。

但是没有类型声明的库呢？在我帖子的第一部分，我看到了这个评论。

[![vignesh0025 profile image](img/e0cfd2b04f29ee1eb1cac8eb691ae717.png) ](/vignesh0025) [ Vignesh D ](/vignesh0025) • [<time datetime="2019-01-09T16:54:28Z"> Jan 9 '19 </time>](https://dev.to/vignesh0025/comment/834k) 

我尝试用 react 和 Evergreen-Ui react 库来使用 Typescript。但是 Evergreen-ui 不支持 typescript。它要的是我看不懂的申报文件。如何进行？

一些包在项目中包含了它们自己的类型，所以它经常会被 TypeScript 编译器选中。但是如果我们既没有内置的类型也没有库的`@types`包，我们可以使用环境声明(`*.d.ts`文件)为这些库创建一个垫片。

首先，在源目录中创建一个文件夹来保存环境声明。叫它`types/`或者别的什么，这样我们就可以很容易地找到它们。接下来，创建一个文件来保存我们自己对该库的自定义声明。通常我们使用库名，例如`evergreen-ui.d.ts`。

现在，在我们刚刚创建的`.d.ts`文件中，放入以下内容:

```
declare module 'evergreen-ui' 
```

Enter fullscreen mode Exit fullscreen mode

这将填补`evergreen-ui`模块，这样我们就可以安全地导入它，而不会出现“找不到模块”的错误。

请注意，这并不支持自动完成，因此您必须手动声明该库的 API。这当然是可选的，但是如果您想要更好的自动完成，这是非常有用的。

例如，如果我们使用 Evergreen UI 的按钮组件:

```
// Import React's base types for us to use.
import * as React from 'react'

declare module 'evergreen-ui' {
  export interface ButtonProps extends DimensionProps, SpacingProps, PositionProps, LayoutProps {
    // The above extended props props are examples for extending common props and are not included in this example for brevity.

    intent: 'none' | 'success' | 'warning' | 'danger'
    appearance: 'default' | 'minimal' | 'primary'
    isLoading?: boolean

    // Again, skipping the rest of the props for brevity, but you get the idea.
  }

  export class Button extends React.PureComponent<ButtonProps> {}
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

这就是第 2 部分！完整的指南到此结束，但是如果在这篇文章发表后还有任何问题，我会在第 3 部分回答其中的一些。

提醒一下，[react flux](https://www.reactiflux.com/)Discord 服务器上的`#typescript`频道有一群对 TypeScript 了如指掌的可爱的人。欢迎随时加入并询问任何关于 TypeScript 的问题！