# 又一个减少 Redux (NGRX)应用中样板文件的指南

> 原文：<https://dev.to/itnext/yet-another-guide-to-reduce-boilerplate-in-your-redux-ngrx-app-163>

> 我现在有了一个闪亮的新博客。阅读这篇文章的最新更新[https://blog . goncharov . page/that-another-guide-to-redux-ngrx-app 中的 reduce-boilerplate](https://blog.goncharov.page/yet-another-guide-to-reduce-boilerplate-in-your-redux-ngrx-app)

## 这里我们要讲些什么？

在我们被样板文件淹没的 Redux(和 NGRX)中，减少样板文件的几种方法/技巧/窍门/古老的黑魔法仪式！)我用多年的第一手制作经验想出来的应用。

伙计们，我跟你们说实话吧。一开始我想讲讲我的新微型图书馆 [flux-action-class](https://github.com/aigoncharov/flux-action-class) ，但是似乎每个人都在抱怨科技博客越来越像 Twitter，每个人都想要一些有意义的长篇阅读等等。所以我想:“管它呢？我有一些自己的经验和最佳实践，我为此付出了汗水和鲜血。也许，它可以帮助那里的一些人。也许，外面的人可以帮我改进一些。”

## 识别样板文件

让我们来看一个如何在 Redux 中进行 AJAX 请求的典型例子。在这个特殊的例子中，让我们假设我们想从服务器上获取一个猫的列表。

```
import { createSelector } from 'reselect'

const actionTypeCatsGetInit = 'CATS_GET_INIT'
const actionTypeCatsGetSuccess = 'CATS_GET_SUCCESS'
const actionTypeCatsGetError = 'CATS_GET_ERROR'

const actionCatsGetInit = () => ({ type: actionTypeCatsGetInit })
const actionCatsGetSuccess = (payload) => ({ type: actionTypeCatsGetSuccess: payload })
const actionCatsGetError = (error) => ({ type: actionTypeCatsGetError, payload: error })

const reducerCatsInitialState = {
  error: undefined,
  data: undefined,
  loading: false,
}
const reducerCats = (state = reducerCatsInitialState, action) => {
  switch (action.type) {
    case actionTypeCatsGetInit:
      return {
        ...state,
        loading: true,
      }
    case actionCatsGetSuccess:
      return {
        error: undefined,
        data: action.payload,
        loading: false,
      }
    case actionCatsGetError:
      return {
        ...data,
        error: action.payload,
        loading: false,
      }
    default:
      return state
  }
}

const makeSelectorCatsData = () =>
  createSelector(
    (state) => state.cats.data,
    (cats) => cats,
  )
const makeSelectorCatsLoading = () =>
  createSelector(
    (state) => state.cats.loading,
    (loading) => loading,
  )
const makeSelectorCatsError = () =>
  createSelector(
    (state) => state.cats.error,
    (error) => error,
  ) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道为什么我有选择器工厂(makeSelector...)这里看一看

我故意省略了副作用处理。这是一篇完全不同的文章的主题，充满了青少年对现有生态系统的愤怒和批评:D

这段代码有几个弱点:

*   动作创建者本身是独特的对象，但是我们仍然需要动作类型来序列化。我们能做得更好吗？
*   当我们添加实体时，我们不断复制翻转`loading`标志的相同逻辑。实际的服务器数据和我们想要处理它的方式可能会改变，但是`loading`的逻辑总是相同的。我们能摆脱它吗？
*   Switch 语句是 O(n)，[类似于](https://stackoverflow.com/questions/4442835/what-is-the-runtime-complexity-of-a-switch-statement)(这本身不是一个可靠的参数，因为 Redux 无论如何都不是很有性能)，需要为每个`case`添加几行额外的代码，并且 switch 不容易组合。我们能想出更具性能和可读性的东西吗？
*   我们真的需要为每个实体分别保留一个错误吗？
*   使用选择器是个好主意。通过这种方式，我们对我们的商店进行了抽象，可以通过调整我们的选择器来改变它的形状，而不会破坏整个应用程序。然而，由于 memoizaion 的工作方式，我们必须为每个选择器创建一个工厂。还有别的办法吗？

## 提示 1:摆脱动作类型

不完全是。但是我们可以让 JS 为我们生成它们！

让我们花一分钟思考一下为什么我们需要动作类型？显然，为了帮助 reducer 以某种方式区分进来的动作并相应地改变我们的状态。但它真的必须是字符串吗？如果我们有办法创建特定类型的对象(动作)就好了...班级来救援！我们绝对可以使用类作为动作创建者，并通过类型来做`switch`。像这样:

```
class CatsGetInit {}
class CatsGetSuccess {
  constructor(responseData) {
    this.payload = responseData
  }
}
class CatsGetError {
  constructor(error) {
    this.payload = error
    this.error = true
  }
}

const reducerCatsInitialState = {
  error: undefined,
  data: undefined,
  loading: false,
}
const reducerCats = (state = reducerCatsInitialState, action) => {
  switch (action.constructor) {
    case CatsGetInit:
      return {
        ...state,
        loading: true,
      }
    case CatsGetSuccess:
      return {
        error: undefined,
        data: action.payload,
        loading: false,
      }
    case CatsGetError:
      return {
        ...data,
        error: action.payload,
        loading: false,
      }
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

一切都好，但有一件事...我们不能再序列化和反序列化我们的行动。它们不再是具有对象原型的简单对象。他们都有独特的原型，这实际上使切换`action.constructor`工作。天啊，我喜欢将我的动作序列化成字符串并将其附加到错误报告中的想法。那么我们能做得更好吗？

其实是的！幸运的是，每个类都有一个名字，这是一个字符串，我们可以利用它们。所以出于序列化的目的，每个动作都需要是一个带有字段`type`的简单对象(请看这里的以了解任何有自尊的动作还应该有什么)。我们可以将字段`type`添加到每个使用类名的类中。

```
class CatsGetInit {
  constructor() {
    this.type = this.constructor.name
  }
}
const reducerCats = (state, action) => {
  switch (action.type) {
    case CatsGetInit.name:
      return {
        ...state,
        loading: true,
      }
    //...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是这样我们就不能像这个伟大的提议所建议的那样给我们的动作类型加前缀(实际上，我更喜欢它的[继任者](https://github.com/alexnm/re-ducks))。为了解决前缀问题，我们应该停止直接使用类名。我们可以做的是为类型创建一个静态 getter 并利用它。

```
class CatsGetInit {
  get static type () {
    return `prefix/${this.name}`
  }
  constructor () {
    this.type = this.constructor.type
  }
}
const reducerCats = (state, action) => {
  switch (action.type) {
    case CatsGetInit.type:
      return {
        ...state,
        loading: true,
      }
    //...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们稍微润色一下以避免代码重复，并增加一个假设以进一步减少样板文件:如果动作是一个错误动作`payload`必须是`Error`的一个实例。

```
class ActionStandard {
  get static type () {
    return `prefix/${this.name}`
  }
  constructor(payload) {
    this.type = this.constructor.type
    this.payload = payload
    this.error = payload instanceof Error
  }
}

class CatsGetInit extends ActionStandard {}
class CatsGetSuccess extends ActionStandard {}
class CatsGetError extends ActionStandard {}

const reducerCatsInitialState = {
  error: undefined,
  data: undefined,
  loading: false,
}
const reducerCats = (state = reducerCatsInitialState, action) => {
  switch (action.type) {
    case CatsGetInit.type:
      return {
        ...state,
        loading: true,
      }
    case CatsGetSuccess.type:
      return {
        error: undefined,
        data: action.payload,
        loading: false,
      }
    case CatsGetError.type:
      return {
        ...data,
        error: action.payload,
        loading: false,
      }
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，它与 NGRX 配合得很好，但是 Redux 抱怨分派非普通对象(它验证原型链)。幸运的是，JS 允许我们从构造函数返回一个任意值，我们并不真的需要我们的动作有一个原型。

```
class ActionStandard {
  get static type () {
    return `prefix/${this.name}`
  }
  constructor(payload) {
    return {
      type: this.constructor.type,
      payload,
      error: payload instanceof Error
    }
  }
}

class CatsGetInit extends ActionStandard {}
class CatsGetSuccess extends ActionStandard {}
class CatsGetError extends ActionStandard {}

const reducerCatsInitialState = {
  error: undefined,
  data: undefined,
  loading: false,
}
const reducerCats = (state = reducerCatsInitialState, action) => {
  switch (action.type) {
    case CatsGetInit.type:
      return {
        ...state,
        loading: true,
      }
    case CatsGetSuccess.type:
      return {
        error: undefined,
        data: action.payload,
        loading: false,
      }
    case CatsGetError.type:
      return {
        ...data,
        error: action.payload,
        loading: false,
      }
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了不让你们复制粘贴`ActionStandard`类并担心它的可靠性，我创建了一个名为 flux-action-class 的[小库，它已经用 100%代码覆盖率的测试覆盖了所有代码，用 TypeScript 为 TypeScript 和 JavaScript 项目编写。](https://github.com/aigoncharov/flux-action-class)

## 技巧 2:结合你的减速器

想法很简单:使用[组合减速器](https://redux.js.org/api/combinereducers)不仅用于顶级减速器，也用于组合`loading`和其他东西的减速器。让代码自己说话:

```
const reducerLoading = (actionInit, actionSuccess, actionError) => (
  state = false,
  action,
) => {
  switch (action.type) {
    case actionInit.type:
      return true
    case actionSuccess.type:
      return false
    case actionError.type:
      return false
  }
}

class CatsGetInit extends ActionStandard {}
class CatsGetSuccess extends ActionStandard {}
class CatsGetError extends ActionStandard {}

const reducerCatsData = (state = undefined, action) => {
  switch (action.type) {
    case CatsGetSuccess.type:
      return action.payload
    default:
      return state
  }
}
const reducerCatsError = (state = undefined, action) => {
  switch (action.type) {
    case CatsGetError.type:
      return action.payload
    default:
      return state
  }
}

const reducerCats = combineReducers({
  data: reducerCatsData,
  loading: reducerLoading(CatsGetInit, CatsGetSuccess, CatsGetError),
  error: reducerCatsError,
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 提示三:开关远离开关

使用对象并通过键从它们中选择！用键选择一个对象的属性是 O(1 ),如果你问我的话，它看起来要干净得多。像这样:

```
const createReducer = (initialState, reducerMap) => (
  state = initialState,
  action,
) => {
  // Pick a reducer from the object by key
  const reducer = reducerMap[action.type]
  if (!reducer) {
    return state
  }
  // Run the reducer if present
  return reducer(state, action)
}

const reducerLoading = (actionInit, actionSuccess, actionError) =>
  createReducer(false, {
    [actionInit.type]: () => true,
    [actionSuccess.type]: () => false,
    [actionError.type]: () => false,
  })

class CatsGetInit extends ActionStandard {}
class CatsGetSuccess extends ActionStandard {}
class CatsGetError extends ActionStandard {}

const reducerCatsData = createReducer(undefined, {
  [CatsGetSuccess.type]: () => action.payload,
})
const reducerCatsError = createReducer(undefined, {
  [CatsGetError.type]: () => action.payload,
})

const reducerCats = combineReducers({
  data: reducerCatsData,
  loading: reducerLoading(CatsGetInit, CatsGetSuccess, CatsGetError),
  error: reducerCatsError,
}) 
```

Enter fullscreen mode Exit fullscreen mode

我建议我们稍微重构一下`reducerLoading`。随着 reducer 映射的引入，从`reducerLoading`返回一个 reducer 映射是有意义的，因此如果需要的话我们可以很容易地扩展它(不像开关)。

```
const createReducer = (initialState, reducerMap) => (
  state = initialState,
  action,
) => {
  // Pick a reducer from the object by key
  const reducer = state[action.type]
  if (!reducer) {
    return state
  }
  // Run the reducer if present
  return reducer(state, action)
}

const reducerLoadingMap = (actionInit, actionSuccess, actionError) => ({
  [actionInit.type]: () => true,
  [actionSuccess.type]: () => false,
  [actionError.type]: () => false,
})

class CatsGetInit extends ActionStandard {}
class CatsGetSuccess extends ActionStandard {}
class CatsGetError extends ActionStandard {}

const reducerCatsLoading = createReducer(
  false,
  reducerLoadingMap(CatsGetInit, CatsGetSuccess, CatsGetError),
)
/*  Now we can easily extend it like this:
    const reducerCatsLoading = createReducer(
      false,
      {
        ...reducerLoadingMap(CatsGetInit, CatsGetSuccess, CatsGetError),
        ... some custom stuff
      }
    )
*/
const reducerCatsData = createReducer(undefined, {
  [CatsGetSuccess.type]: () => action.payload,
})
const reducerCatsError = createReducer(undefined, {
  [CatsGetError.type]: () => action.payload,
})

const reducerCats = combineReducers({
  data: reducerCatsData,
  loading: reducerCatsLoading),
  error: reducerCatsError,
}) 
```

Enter fullscreen mode Exit fullscreen mode

[Redux 的官方文档提到了这个](https://redux.js.org/recipes/reducing-boilerplate#generating-reducers)，但是出于某种原因，我看到很多人仍然在使用 switch-cases。已经有一个[图书馆](https://github.com/kolodny/redux-create-reducer)供`createReducer`使用。不要犹豫使用它。

## 提示 4:拥有一个全局错误处理程序

完全没有必要为每个实体单独保留一个错误，因为在大多数情况下，我们只需要显示一个错误对话框之类的东西。所有的错误对话框都是一样的！

创建一个全局错误处理程序。在最简单的情况下，它可能是这样的:

```
class GlobalErrorInit extends ActionStandard {}
class GlobalErrorClear extends ActionStandard {}

const reducerError = createReducer(undefined, {
  [GlobalErrorInit.type]: (state, action) => action.payload,
  [GlobalErrorClear.type]: (state, action) => undefined,
}) 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的副作用的`catch`里屏蔽掉`ErrorInit`。用[的 redux-thunk](https://github.com/reduxjs/redux-thunk) :
可以是这样的

```
const catsGetAsync = async (dispatch) => {
  dispatch(new CatsGetInit())
  try {
    const res = await fetch('https://cats.com/api/v1/cats')
    const body = await res.json()
    dispatch(new CatsGetSuccess(body))
  } catch (error) {
    dispatch(new CatsGetError(error))
    dispatch(new GlobalErrorInit(error))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以停止为猫状态的`error`部分和仅仅翻转`loading`标志的`CatsGetError`部分提供缩减器。

```
class CatsGetInit extends ActionStandard {}
class CatsGetSuccess extends ActionStandard {}
class CatsGetError extends ActionStandard {}

const reducerCatsLoading = createReducer(
  false,
  reducerLoadingMap(CatsGetInit, CatsGetSuccess, CatsGetError),
)
const reducerCatsData = createReducer(undefined, {
  [CatsGetSuccess.type]: () => action.payload,
})

const reducerCats = combineReducers({
  data: reducerCatsData,
  loading: reducerCatsLoading)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 技巧 5:停止回忆一切

让我们再一次看看选择器带来的混乱。

*我省略了`makeSelectorCatsError`，因为我们在前一章已经发现了。*

```
const makeSelectorCatsData = () =>
  createSelector(
    (state) => state.cats.data,
    (cats) => cats,
  )
const makeSelectorCatsLoading = () =>
  createSelector(
    (state) => state.cats.loading,
    (loading) => loading,
  ) 
```

Enter fullscreen mode Exit fullscreen mode

为什么我们要为所有东西创建记忆选择器？有什么值得纪念的？通过键选择一个对象的字段(这正是这里所发生的)是 O(1)。只需编写一个常规的非记忆函数。只有当您希望以一种在将数据返回到组件之前需要非常量时间的方式来更改存储中数据的形状时，才使用内存化。

```
const selectorCatsData = (state) => state.cats.data
const selectorCatsLoading = (state) => state.cats.loading 
```

Enter fullscreen mode Exit fullscreen mode

只有计算出一些派生数据，记忆才有意义。对于这个例子，让我们假设每只猫都是一个字段为`name`的对象，我们需要一个包含所有猫的名字的字符串。

```
const makeSelectorCatNames = () =>
  createSelector(
    (state) => state.cats.data,
    (cats) => cats.data.reduce((accum, { name }) => `${accum}  ${name}`, ''),
  ) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

让我们来看看我们从什么开始:

```
import { createSelector } from 'reselect'

const actionTypeCatsGetInit = 'CATS_GET_INIT'
const actionTypeCatsGetSuccess = 'CATS_GET_SUCCESS'
const actionTypeCatsGetError = 'CATS_GET_ERROR'

const actionCatsGetInit = () => ({ type: actionTypeCatsGetInit })
const actionCatsGetSuccess = (payload) => ({
  type: actionTypeCatsGetSuccess,
  payload,
})
const actionCatsGetError = (error) => ({
  type: actionTypeCatsGetError,
  payload: error,
})

const reducerCatsInitialState = {
  error: undefined,
  data: undefined,
  loading: false,
}
const reducerCats = (state = reducerCatsInitialState, action) => {
  switch (action.type) {
    case actionTypeCatsGetInit:
      return {
        ...state,
        loading: true,
      }
    case actionCatsGetSuccess:
      return {
        error: undefined,
        data: action.payload,
        loading: false,
      }
    case actionCatsGetError:
      return {
        ...data,
        error: action.payload,
        loading: false,
      }
    default:
      return state
  }
}

const makeSelectorCatsData = () =>
  createSelector(
    (state) => state.cats.data,
    (cats) => cats,
  )
const makeSelectorCatsLoading = () =>
  createSelector(
    (state) => state.cats.loading,
    (loading) => loading,
  )
const makeSelectorCatsError = () =>
  createSelector(
    (state) => state.cats.error,
    (error) => error,
  ) 
```

Enter fullscreen mode Exit fullscreen mode

结果是什么:

```
class CatsGetInit extends ActionStandard {}
class CatsGetSuccess extends ActionStandard {}
class CatsGetError extends ActionStandard {}

const reducerCatsLoading = createReducer(
  false,
  reducerLoadingMap(CatsGetInit, CatsGetSuccess, CatsGetError),
)
const reducerCatsData = createReducer(undefined, {
  [CatsGetSuccess.type]: () => action.payload,
})

const reducerCats = combineReducers({
  data: reducerCatsData,
  loading: reducerCatsLoading)
})

const selectorCatsData = (state) => state.cats.data
const selectorCatsLoading = (state) => state.cats.loading 
```

Enter fullscreen mode Exit fullscreen mode

希望你找到了对你的项目有用的东西。请随时将您的反馈反馈给我！我非常感谢任何批评和问题。