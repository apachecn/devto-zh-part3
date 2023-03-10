# 介绍 Redux 预打包:类型脚本友好的 Redux 帮助器

> 原文：<https://dev.to/denisw/introducing-redux-preboiled-typescript-friendly-redux-helpers-3b05>

无论是在 [Futurice](https://www.futurice.com/) 还是在我的业余时间，我都在基于 [Redux](https://redux.js.org) 开发应用。通常，Redux 部分倾向于积累大量样板代码——动作类型常量、动作创建者声明、带有相同`default: return state`子句的 reducer `switch`语句，等等。

一些代码库有专门的助手函数来减少噪音，但是这些函数通常很少被记录，并且没有足够的通用性来跨项目重用。如果我们的项目可以依赖现有的维护良好的 Redux helpers 库，那就太好了。然而，每当我寻找这样的库时，我都没有找到与我的要求完全匹配的内容。

基本上，我一直在寻找一个*"[Lodash](https://lodash.com/)for Redux "*-一个简单、独立的助手函数的集合，我可以根据我的项目需求从中挑选，而不是像 [Rematch](https://github.com/rematch/rematch) 这样的一体化框架(这很好，但太固执己见，无法适应所有用例)。这些助手需要很好地与 [TypeScript](https://typescriptlang.org) 一起工作，因为我的工作越来越依赖于它——这是许多 Redux 库(其中许多是 TypeScript 之前的库)难以满足的要求。最后，我通常希望避免引入像[模型和实体](https://github.com/redux-orm/redux-orm)这样的新概念的精神负担；在大多数情况下，我只是需要一些现有 Redux 概念之上的便利。

由于我找不到现有的解决方案，我开始创建自己的解决方案。我很高兴地宣布，我最近发布了这项工作的成果，名为 **Redux Preboiled** 。

### 打字友好的 Redux 助手，提供*点菜*

Redux Preboiled 是一个减少样板文件的 Redux helper 函数库，旨在很好地组合在一起，同时仍然可以单独使用。它是用 TypeScript 编写的，并针对 TypeScript 进行了优化，同时仍然非常适合纯 JavaScript 应用程序。预融合的一个主要目标是简单——没有花哨的新概念，不依靠聪明的“魔法”以牺牲可理解性为代价删除几行代码，而只是简单明了的小函数，可以很容易地组合起来做更大的事情。

让我们看一个例子——事实上，让我们看看 Redux 世界中所有例子的例子，好老的[计数器](https://redux.js.org/introduction/examples#counter-vanilla)。为了更加刺激，我们将加入一个参数化的`multiply`动作来补充`increment`。假设你遵循了 Redux 文档中推荐的模式，那么你可以用 vanilla Redux 来写这个:

```
// Action Types

const INCREMENT = 'increment'
const MULTIPLY = 'multiply'

// Action Creators

const increment = () => ({
  type: INCREMENT
})

const multiply = amount => ({
  type: MULTIPLY,
  payload: amount
})

// Reducer

const counterReducer = (state = 0, action) => {
  switch (action.type) {
    case INCREMENT:
      return state + 1
    case MULTIPLY:
      return state * action.payload
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这一点也不可怕，但这里已经有一些绒毛。注意，我们不得不为动作类型写两个声明——一个动作类型常量和一个相应的动作创建函数。当然，这种方法有几个好处，但是仍然很麻烦。此外，对于其他像`counterReducer`这样的缩减器，我们需要重复`switch (action.type) { … }`仪式，并确保不要忘记最后的`default: return state`条款。

使用 Redux Preboiled，您可以这样写:

```
import {
  chainReducers,
  createAction,
  onAction,
  withInitialState
} from 'redux-preboiled'

// Actions

const increment = createAction('increment')
const multiply = createAction('multiply').withPayload()

// Reducer

const counterReducer = chainReducers(
  withInitialState(0),
  onAction(increment, state => state + 1),
  onAction(multiply, (state, action) => state * action.payload)
) 
```

Enter fullscreen mode Exit fullscreen mode

我们将行数从 28 行减少到 19 行，其中 6 行是 import 语句。这意味着，忽略导入(这在这里很重要，因为这个例子很小)，Preboiled 帮助我们将 action 和 reducer 定义的长度减少了一半，这要感谢以下四个助手:

*   `createAction`，其生成给定动作类型(可选地具有`payload`参数)*的动作创建者，并且*使得该动作类型作为该动作创建者(例如`increment.type`)上的`type`属性可用，使得不需要单独的动作类型常量；

*   `onAction`和`withInitialState`，分别用于创建处理特定动作类型或提供初始状态的子归约器；

*   和`chainReducers`，它通过将我们的子缩减器安排到一个管道(或“调用链”)来将它们集合在一起。

从上面可以看出，这些助手被设计成彼此配合得很好。例如，`onAction`允许我们通过传递一个我们用`createAction`生成的动作创建者(例如`increment`)来指定动作类型，而不是必须显式地传递相应的动作类型(`increment.type`或`'increment'`)。后一个选项也是可用的——使`onAction`可以独立使用——但是如果您一起使用两个助手，前一个选项会提供额外的便利。

### 适合打字稿

从一开始，Redux Preboiled 的设计就考虑到了 TypeScript。助手的函数签名非常适合精确的静态类型，我尽可能地利用自动类型推断的机会。

举个例子，我们再来看看`onAction`。如前所述，它接受一个`createAction`动作创建者来代替一个动作类型。我添加这个选项不仅是为了方便，也是出于打字的原因:因为动作创建者的静态类型包含了它所产生的动作的形状，所以 TypeScript 的编译器可以推断出状态更新函数的`action`参数的类型，而无需您指定它。如果您显式指定的参数类型与推断的类型不兼容，它也会给出一个错误。

```
// TypeScript

import { createAction, onAction } from 'redux-preboiled'

const multiply = createAction('multiply').withPayload<number>()

const onMultiply1 = onAction(multiply, (state: number, action) => {
  // `action` is inferred to be of type
  // PayloadAction<number, 'multiply'>.
  return state + action.payload
})

const onMultiply1 = onAction(
  multiply,
    (state, action: PayloadAction<string>) => {
    // ERROR: Argument of type
    // 'PayloadActionCreator<number, "multiply">'
    // is not assignable to parameter of type
    // 'TypedActionCreator<string, any>'.
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

请注意在上面的代码片段中，`createAction(…).withPayload()`允许我们使用类型参数来指定有效载荷的类型——这是预编译帮助您保持代码类型安全的另一种方式。

### Redux 入门套件怎么样？

大约一年前，Redux 核心维护者[马克·埃里克森](https://redux-starter-kit.js.org)发布了 [Redux 初学者工具包](https://redux-starter-kit.js.org)，这是一个让 Redux 变得更高效的库。它类似于 Redux 预模糊，因为它也包括一堆帮助函数，包括一个启发了预模糊等价的 [`createAction`](https://redux-starter-kit.js.org/api/createaction) 函数。其实在我开始 Preboiled 之前，我就帮[把 Redux Starter Kit 移植到 TypeScript](https://github.com/reduxjs/redux-starter-kit/pull/73) 。

然而，这两个库在方法上有些不同。Redux Starter Kit 背后的愿景是成为 Redux 的“[create-react-app](https://github.com/facebook/create-react-app)”——一个自以为是的即插即用 Redux 设置，让您立即投入运行，而不必浪费时间在琐碎的任务上，如集成[开发工具](https://github.com/reduxjs/redux-devtools)或决定副作用库( [Redux Thunk](https://github.com/reduxjs/redux-thunk) 是预配置的)。当然，另一方面，你可能会得到比你想要的更多或不同的东西；例如，Redux Starter Kit 的`createReducer`助手将 [Immer](https://github.com/mweststrate/immer) 用于简化的不可变更新，即使您对利用这种集成没有兴趣。

另一方面，Redux Preboiled 更像是一种工具包方法。它对设置 Redux 商店或选择中间件没有帮助。相反，它只是给你一套有用的工具，你可以根据需要使用或忽略其中的每一个。这种灵活性带来了一点不便，但是使这个库对更多的 Redux 项目有用。

最终，Redux Starter Kit 和 Preboiled 并不是真正冲突的选择。你可以在用前者建立的商店上使用后者。我也相信交叉授粉有很大的空间，我可以看到我自己为初学者工具包贡献一些预煮的东西，如果它们很合适的话。

### 准备上菜

Redux Preboiled 的 0.1.0 版本现在[在 NPM](https://npmjs.com/redux-preboiled) 和 [GitHub](https://github.com/denisw/redux-preboiled) 上可用。当前的帮助器集仍然很小，但是随着时间的推移，将会覆盖更多的 Redux 样板来源。

首先，查看一下[文档](https://redux-preboiled.js.org/docs)，其中包括几个指南和所有助手的详细 API 文档。尽情享受吧！