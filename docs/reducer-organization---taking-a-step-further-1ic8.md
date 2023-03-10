# 减速器机构-更进一步

> 原文：<https://dev.to/itnext/reducer-organization---taking-a-step-further-1ic8>

> 我现在有了一个闪亮的新博客。阅读这篇文章的最新更新[https://blog . goncharov . page/reducer-organization-taking-a-step-further](https://blog.goncharov.page/reducer-organization-taking-a-step-further)
> 
> 如果你对在 Redux / NGRX 中处理太多的样板文件感兴趣，你可能想看看这篇文章。
> 
> 如果你已经熟悉从 map 技术中选择一个缩减器，考虑直接跳到[基于类的缩减器](#class-based-reducers)。

## 这里我们要讲些什么？

我们将概述 Redux/NGRX 应用程序中减速器在过去两年中的发展。从普通的`switch-case`开始，到通过键从对象中选择一个缩减器，最后解决基于类的缩减器。我们不仅要讨论如何做，还要讨论为什么做。

## 香草开关盒

所以让我们来看看在服务器上异步创建一个实体的日常任务。这次我建议我们描述一下如何创造一个新的绝地。

```
const actionTypeJediCreateInit = 'jedi-app/jedi-create-init'
const actionTypeJediCreateSuccess = 'jedi-app/jedi-create-success'
const actionTypeJediCreateError = 'jedi-app/jedi-create-error'

const reducerJediInitialState = {
  loading: false,
  // List of our jedi
  data: [],
  error: undefined,
}
const reducerJedi = (state = reducerJediInitialState, action) => {
  switch (action.type) {
    case actionTypeJediCreateInit:
      return {
        ...state,
        loading: true,
      }
    case actionTypeJediCreateSuccess:
      return {
        loading: false,
        data: [...state.data, action.payload],
        error: undefined,
      }
    case actionTypeJediCreateError:
      return {
        ...state,
        loading: false,
        error: action.payload,
      }
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

老实说，我从未在生产中使用过这种减速器。我的理由有三点:

*   `switch-case`介绍了一些紧张点，泄漏的管道，我们可能会在某些时候忘记及时修补。如果不立即执行`return`，我们可能总是忘记添加`break`，我们可能总是忘记添加`default`，我们必须将它添加到每个减速器中。
*   有一些样板代码本身没有添加任何上下文。
*   `switch-case`是 O(n)，[种](https://stackoverflow.com/questions/4442835/what-is-the-runtime-complexity-of-a-switch-statement)。这本身不是一个可靠的论点，因为 Redux 无论如何都不是很有表现力，但它让我内心的完美主义者抓狂。

Redux 的官方文档建议采取的合乎逻辑的下一步是通过键从对象中选择一个缩减器。

## 通过按键从对象中选择一个减速器

这个想法很简单。每个状态转换都是状态和动作的函数，并有相应的动作类型。考虑到每个动作类型是一个字符串，我们可以创建一个对象，其中每个键是一个动作类型，每个值是一个转换状态的函数(一个缩减器)。然后，当我们收到一个新的动作时，我们可以通过键从该对象中选择一个所需的缩减器，即 O(1)。

```
const actionTypeJediCreateInit = 'jedi-app/jedi-create-init'
const actionTypeJediCreateSuccess = 'jedi-app/jedi-create-success'
const actionTypeJediCreateError = 'jedi-app/jedi-create-error'

const reducerJediInitialState = {
  loading: false,
  data: [],
  error: undefined,
}
const reducerJediMap = {
  [actionTypeJediCreateInit]: (state) => ({
    ...state,
    loading: true,
  }),
  [actionTypeJediCreateSuccess]: (state, action) => ({
    loading: false,
    data: [...state.data, action.payload],
    error: undefined,
  }),
  [actionTypeJediCreateError]: (state, action) => ({
    ...state,
    loading: false,
    error: action.payload,
  }),
}

const reducerJedi = (state = reducerJediInitialState, action) => {
  // Pick a reducer by action type
  const reducer = reducerJediMap[action.type]
  if (!reducer) {
    // Return state unchanged if we did not find a suitable reducer
    return state
  }
  // Run suitable reducer if found one
  return reducer(state, action)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里最酷的事情是`reducerJedi`内部的逻辑对于任何缩减器都保持不变，这意味着我们可以重用它。甚至有一个名为 [redux-create-reducer](https://github.com/kolodny/redux-create-reducer) 的小库，它就是这样做的。它使代码看起来像这样:

```
import { createReducer } from 'redux-create-reducer'

const actionTypeJediCreateInit = 'jedi-app/jedi-create-init'
const actionTypeJediCreateSuccess = 'jedi-app/jedi-create-success'
const actionTypeJediCreateError = 'jedi-app/jedi-create-error'

const reducerJediInitialState = {
  loading: false,
  data: [],
  error: undefined,
}
const reducerJedi = createReducer(reducerJediInitialState, {
  [actionTypeJediCreateInit]: (state) => ({
    ...state,
    loading: true,
  }),
  [actionTypeJediCreateSuccess]: (state, action) => ({
    loading: false,
    data: [...state.data, action.payload],
    error: undefined,
  }),
  [actionTypeJediCreateError]: (state, action) => ({
    ...state,
    loading: false,
    error: action.payload,
  }),
}) 
```

Enter fullscreen mode Exit fullscreen mode

很漂亮，是吧？尽管这仍然有一些警告:

*   如果是复杂的减速器，我们必须留下大量的注释来描述这个减速器做什么以及为什么。
*   巨大的减速器图很难读懂。
*   每个减速器只有一个对应的动作类型。如果我想让同一个减速器运行几个动作呢？

基于类的减速器成了我在黑夜王国里的光棚。

## 基于类的减速器

这一次，让我从这种方法的原因开始:

*   “类”方法将是我们的缩减器，方法有名字，这是一个有用的元信息，我们可以在 90%的情况下放弃注释。
*   “Class”方法可以被修饰，这是一种易于阅读的声明方式来匹配动作和 reducers。
*   我们仍然可以使用一个引擎盖下的行动图来获得 O(1)复杂度。

如果这听起来像是一个合理的理由列表，让我们开始吧！

首先，我想定义一下我们想要得到的结果。

```
const actionTypeJediCreateInit = 'jedi-app/jedi-create-init'
const actionTypeJediCreateSuccess = 'jedi-app/jedi-create-success'
const actionTypeJediCreateError = 'jedi-app/jedi-create-error'

class ReducerJedi {
  // Take a look at "Class field delcaratrions" proposal, which is now at Stage 3.
  // https://github.com/tc39/proposal-class-fields
  initialState = {
    loading: false,
    data: [],
    error: undefined,
  }

  @Action(actionTypeJediCreateInit)
  startLoading(state) {
    return {
      ...state,
      loading: true,
    }
  }

  @Action(actionTypeJediCreateSuccess)
  addNewJedi(state, action) {
    return {
      loading: false,
      data: [...state.data, action.payload],
      error: undefined,
    }
  }

  @Action(actionTypeJediCreateError)
  error(state, action) {
    return {
      ...state,
      loading: false,
      error: action.payload,
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们看到我们想要达到的目标时，我们可以一步一步来。

### 第一步。@动作装饰师。

这里我们想做的是接受任意数量的动作类型，并将它们作为元信息存储起来，供类的方法以后使用。为此，我们可以利用[Reflect-metadata](https://github.com/rbuckton/reflect-metadata)poly fill，它为 [Reflect](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect) 对象带来了元数据功能。之后，这个装饰器会把它的参数(动作类型)作为元数据附加到一个方法上。

```
const METADATA_KEY_ACTION = 'reducer-class-action-metadata'

export const Action = (...actionTypes) => (target, propertyKey, descriptor) => {
  Reflect.defineMetadata(METADATA_KEY_ACTION, actionTypes, target, propertyKey)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步。从一个缩减器类中创建一个缩减器函数

正如我们所知，每个 reducer 都是一个纯粹的函数，它接受一个状态和一个动作，并返回一个新的状态。嗯，类也是一个函数，但是 ES6 类没有`new`是不能被调用的，我们必须用一些方法从一个类中产生一个实际的缩减器。所以我们需要以某种方式改变它。

我们需要一个函数来处理我们的类，遍历每个方法，收集动作类型的元数据，构建一个 reducer 映射，并从 reducer 映射中创建一个最终的 reducer。

下面是我们如何检查一个类的每个方法。

```
const getReducerClassMethodsWthActionTypes = (instance) => {
  // Get method names from class' prototype
  const proto = Object.getPrototypeOf(instance)
  const methodNames = Object.getOwnPropertyNames(proto).filter(
    (name) => name !== 'constructor',
  )

  // We want to get back a collection with action types and corresponding reducers
  const res = []
  methodNames.forEach((methodName) => {
    const actionTypes = Reflect.getMetadata(
      METADATA_KEY_ACTION,
      instance,
      methodName,
    )
    // We want to bind each method to class' instance not to lose `this` context
    const method = instance[methodName].bind(instance)
    // We might have many action types associated with a reducer
    actionTypes.forEach((actionType) =>
      res.push({
        actionType,
        method,
      }),
    )
  })
  return res
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们希望将收到的集合处理成一个 reducer 映射。

```
const getReducerMap = (methodsWithActionTypes) =>
  methodsWithActionTypes.reduce((reducerMap, { method, actionType }) => {
    reducerMap[actionType] = method
    return reducerMap
  }, {}) 
```

Enter fullscreen mode Exit fullscreen mode

所以最终的函数可能是这样的。

```
import { createReducer } from 'redux-create-reducer'

const createClassReducer = (ReducerClass) => {
  const reducerClass = new ReducerClass()
  const methodsWithActionTypes = getReducerClassMethodsWthActionTypes(
    reducerClass,
  )
  const reducerMap = getReducerMap(methodsWithActionTypes)
  const initialState = reducerClass.initialState
  const reducer = createReducer(initialState, reducerMap)
  return reducer
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以像这样把它应用到我们的`ReducerJedi`类中。

```
const reducerJedi = createClassReducer(ReducerJedi) 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步。融合在一起。

```
// We move that generic code to a dedicated module
import { Action, createClassReducer } from 'utils/reducer-class'

const actionTypeJediCreateInit = 'jedi-app/jedi-create-init'
const actionTypeJediCreateSuccess = 'jedi-app/jedi-create-success'
const actionTypeJediCreateError = 'jedi-app/jedi-create-error'

class ReducerJedi {
  // Take a look at "Class field delcaratrions" proposal, which is now at Stage 3.
  // https://github.com/tc39/proposal-class-fields
  initialState = {
    loading: false,
    data: [],
    error: undefined,
  }

  @Action(actionTypeJediCreateInit)
  startLoading(state) {
    return {
      ...state,
      loading: true,
    }
  }

  @Action(actionTypeJediCreateSuccess)
  addNewJedi(state, action) {
    return {
      loading: false,
      data: [...state.data, action.payload],
      error: undefined,
    }
  }

  @Action(actionTypeJediCreateError)
  error(state, action) {
    return {
      ...state,
      loading: false,
      error: action.payload,
    }
  }
}

export const reducerJedi = createClassReducer(ReducerJedi) 
```

Enter fullscreen mode Exit fullscreen mode

## 下一步

以下是我们遗漏的内容:

*   同一个动作对应几个方法怎么办？目前的逻辑不处理这个。
*   我们可以加上 [immer](https://github.com/mweststrate/immer) 吗？
*   如果我使用基于类的动作呢？我怎么能传递一个动作创建者，而不是一个动作类型？

所有附加的代码样本和例子都包含在 [reducer 类](https://github.com/aigoncharov/reducer-class)中。

我必须说为 reducers 使用类并不是一个原创的想法。很久以前，@amcdnl 提出了令人敬畏的 [ngrx-actions](https://github.com/amcdnl/ngrx-actions) ，但是看起来他现在专注于 [NGXS](https://github.com/ngxs/store) ，更不用说我想要更严格的类型和与特定角度逻辑的解耦。[这里列出了 reducer-class 和 ngrx-actions 之间的主要区别。](https://github.com/aigoncharov/reducer-class#how-does-it-compare-to-ngrx-actions)

> 如果你喜欢为你的 reducers 使用类的想法，你可能会喜欢为你的 action creators 做同样的事情。看一看 [flux-action-class](https://github.com/aigoncharov/flux-action-class) 。

希望你找到了对你的项目有用的东西。请随时将您的反馈传达给我！我非常感谢任何批评和问题。