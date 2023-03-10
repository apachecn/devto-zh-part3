# 如何:mobx-状态-树+ react + typescript

> 原文：<https://dev.to/margaretkrutikova/how-to-mobx-state-tree-react-typescript-3d5j>

这是一个关于如何在一个带有`typescript`的`CRA`应用程序中获得带有`mobx-state-tree`和`react`的完整设置的演练。本指南没有过多关注理论或事物如何在引擎盖下工作，主要包括实际例子(代码！)上*如何*让事情运转起来。

在我所有的工作和兼职项目中，我大部分时间都在使用`redux`，最终我对`mobx`产生了好奇，并决定直接进入`mobx-state-tree`。

试图让`mobx-state-tree`和`typescript`在`react`合作似乎是一场相当大的斗争。尤其是让所有东西都正确输入(不要用`any`作弊！)在`Typescript`是一个挑战，所以当最终一切就绪时，我想我会分享我的设置，以便(希望)让别人的生活更轻松:)

我构建的应用程序是一个简单的民意测验生成器，允许创建新的民意测验，发布它，查看和删除已发布的民意测验。我的 [github](https://github.com/MargaretKrutikova/mst-react-ts-guide) 上有带可爱小演示的源代码。

如果您有涉及的特定问题，可以直接跳转到以下快速链接:

*   [在 mobx 状态树中设置商店](#setup-stores-in-mobx-state-tree)
    *   [创建基础模型](#create-a-base-model)
    *   [使用合成创建域存储](#use-composition-to-create-domain-stores)
    *   [嵌套列表中模型上的积垢](#crud-on-models-in-a-nested-list)
    *   [型号间转换](#convert-between-models)
    *   [根存储](#root-store)
    *   [店铺间沟通](#communicate-between-stores)
*   [连接反应到 mobx](#connect-react-to-mobx)
    *   [为什么不是 mobx-react](#why-not-mobx-react)
    *   [mobx-react-lite 前来救援](#mobx-react-lite-to-the-rescue)
    *   [要传递存储的上下文提供者](#context-provider-to-pass-store)
    *   [自定义挂钩注入店铺](#custom-hook-to-inject-stores)

## 在 mobx-state-tree 中设置商店

我在`mobx-state-tree`开始开发我的应用程序，设计领域领域的商店，并立即面临以下“如何做”:

*   如何创建一个基本模型，并使用组合在不同的商店中用属性和功能来扩展它，
*   如何创建一个包含表示另一个模型的嵌套项目列表的商店，并对其执行 CRUD 操作，
*   如何创建包含所有其他域存储的根存储，
*   店铺之间如何沟通？

我认为这些可能是在为任何领域设计商店时的常见问题，所以我将更详细地讨论它们并展示我的解决方案。

在我的 poll-maker 应用程序中，将有一个基本模型`PollBase`，一个负责创建新投票的商店`PollDraft`，一个已发布投票的模型`PublishedPoll`和一个已发布投票的商店`PublishedPolls`。

### 创建基础模型

在我们开始之前，安装必要的依赖项:

```
yarn add mobx mobx-state-tree 
```

现在让我们为域对象`poll`创建一个基本模型，它将有一个投票问题和一个选项列表，以及一个带有字符串属性和 id:
的基本选择模型

```
import { types } from "mobx-state-tree"

const PollChoiceBase = types.model("PollChoiceBase", {
  id: types.identifier,
  value: types.optional(types.string, "")
})

const PollBase = types.model("PollBase", {
  question: "",
  choices: types.optional(types.array(PollChoiceBase), [])
}) 
```

### 使用组合创建域商店

正在编辑(姑且称之为草稿投票)且尚未发布的投票将具有与`PollBase`相同的属性，但也具有编辑这些属性的操作。类似地，一个投票草案的选择将具有与`PollChoiceBase`相同的形状，并带有一个更新它的动作:

```
const PollDraftChoice = PollChoiceBase.actions(self => ({
  setChoice(choice: string) {
    self.value = choice
  }))

const PollDraft = types
  .compose(PollBase,
    types.model({
      choices: types.optional(types.array(PollDraftChoice), [])
    })
  )
  .actions(self => ({
    setQuestion(question: string) {
      self.question = question
    }
})) 
```

已发布的投票不能再被编辑，所以它没有编辑动作，但是它需要一个额外的属性`id`来找到它或创建一个外部链接到它:

```
const PublishedPoll = types.compose(
  PollBase,
  types.model({
    id: types.identifier
  })
) 
```

### 嵌套列表中模型上的 CRUD

投票草稿有一个选项列表，可以添加、编辑和删除。目前我们有一个更新选择的动作(`setChoice`)，但是没有删除现有选择或添加新选择的动作。

在这里，添加相当简单，但是删除有点棘手。我们希望能够在`react`组件中的某个地方使用`choice.remove()`,但是动作只能修改它们所属的模型或它们的子模型，所以选择不能简单地删除自己，只能被它的父`PollDraft`删除，因为它“拥有”选择列表。这意味着`PollDraftChoice`模型将需要一个`remove`动作，该动作将把它的移除委托给`PollDraft`，我们可以通过`getParent`助手从`mobx-state-tree`中检索该动作。

下面是代码(我使用 [shortid](https://github.com/dylang/shortid) 来生成唯一的 id):

```
import { destroy, getParent, Instance, cast } from "mobx-state-tree"

// Instance is a typescript helper that extracts the type of the model instance
type PollDraftChoiceModel = Instance<typeof PollDraftChoice>
type PollDraftModel = Instance<typeof PollDraft>

const PollDraftChoice = PollChoiceBase.actions(self => ({
  ...
  remove() {
    const pollDraftParent = getParent<PollDraftModel>(self, 2)
    pollDraftParent.removeChoice(cast(self))
  }
}))

const PollDraft = types.compose(...)
  .actions(self => ({
    ...
    addChoice(choice: string) {
      self.choices.push({ id: shortid(), value: choice })
    },
    removeChoice(choiceToRemove: PollDraftChoiceModel) {
      destroy(choiceToRemove)
    }
})) 
```

下面是在`PollDraftChoice`里面发生的事情:

*   `getParent<PollDraftModel>(self, 2)`的意思是向上取父节点 2 层——一层直到到达`items`属性，再一层直到到达`PollDraft`本身，假设返回的父节点是类型`PollDraftModel`。
*   `pollDraftParent.removeChoice(cast(self))`使用 [`cast`](https://github.com/mobxjs/mobx-state-tree/blob/master/docs/API/README.md#cast) 助手告诉 typescript】确实属于`PollDraftChoiceModel`类型。为什么有必要？问题是这里的`self`是在视图和动作被应用之前的[类型，这意味着在那一点上`self`实际上不是`PollDraftChoiceModel`类型，所以`pollDraftParent.removeChoice(self)`不会在 TS 中编译。](https://github.com/mobxjs/mobx-state-tree#typing-self-in-actions-and-views)

### 在型号间转换

让我们创建第二个域存储来跟踪发布的投票:

```
import { types, Instance, getSnapshot } from "mobx-state-tree"

type PublishedPollModel = Instance<typeof PublishedPoll>
type PollDraftModel = Instance<typeof PollDraft>

export const PublishedPolls = types
  .model({
    polls: types.optional(types.array(PublishedPoll), [])
  })
  .actions(self => ({
    publishDraft(pollDraft: SnapshotIn<PollDraftModel>) {
      const pollToPublish = { ...pollDraft, id: shortid() }
      self.polls.push(pollToPublish)
    }
  })) 
```

这里`publishDraft`接收一份`snapshot`的投票草案。[`mobx-state-tree`中的](https://github.com/mobxjs/mobx-state-tree#snapshots)快照是一个剥离了所有类型信息和动作的普通对象，可以自动转换成模型。

那么，为什么`publishDraft`需要获取快照，而不仅仅是`PollDraftModel`呢？这是因为`PollDraftModel`的一个实例不能被转换成一个已发布的投票，因为它会有与`PublishedPollModel`不兼容的额外动作，并且会导致运行时异常。因此，通过指定`SnapshotIn<PollDraftModel>`，我们明确地说我们想要存在于`PollDraftModel`的原始数据。

下一个问题是`publishDraft`动作必须从外部的某个地方调用，要么来自`PollDraft`商店，要么来自某种`RootStore`。让我们看看如何实现这一点，并在两家商店之间建立一些沟通。

### 根店

让我们创建一个根商店来合并应用程序中使用的所有商店:`PollDraft`和`PublishedPolls` :

```
type RootStoreModel = Instance<typeof RootStore>

const RootStore = types.model("RootStore", {
  pollDraft: PollDraft,
  publishedPolls: PublishedPolls
}) 
```

### 门店之间的沟通

存储之间的一种通信方式是使用来自`mobx-state-tree`的`getRoot`来获取根存储，并从那里获得必要的存储，或者使用`getParent`来遍历树。这对于紧密耦合的商店(如`PollDraft`和`PollDraftChoice`)很好，但是如果用于更松散的商店，就不能扩展。

启用商店通信的一种方式是利用`getEnv`函数，该函数可以在创建状态树时注入环境特定数据(来自 [mobx-state-tree docs](https://github.com/mobxjs/mobx-state-tree#dependency-injection) )。所以我们可以将一个新创建的存储注入到整个状态树中。这里需要注意的一点是，环境不能直接传递到其中一个子存储中，而需要传递到根存储中，否则会出现以下错误:

```
Error: [mobx-state-tree] A state tree cannot be made part of another state tree 
as long as their environments are different. 
```

让我们创建一个名为`createStore`的函数，类似于`redux`的`configureStore`，它将创建所有单独的商店，创建环境并将它们全部组装到一个根商店中。该环境将只有一个属性`PublishedPolls` store，因为它需要在发布投票草案时从`PollDraft`访问:

```
type RootStoreEnv = {
  publishedPolls: PublishedPollsModel
}

const createStore = (): RootStoreModel => {
  const publishedPolls = PublishedPolls.create()
  const pollDraft = PollDraft.create()

  const env: RootStoreEnv = { publishedPolls }

  return RootStore.create({ pollDraft, publishedPolls }, env)
} 
```

现在，`PolLDraft` store 可以定义一个`publish`动作，并在`publishedPolls` :
上调用`publishDraft`

```
import { types, getEnv, getSnapshot } from "mobx-state-tree"

const PollDraft = types
  .compose(...)
  .actions(self => ({
    ...
    publish() {
      const snapshot = getSnapshot(self)

      const env = getEnv<RootStoreEnv>(self)
      env.publishedPolls.publishDraft(snapshot)
    }
  })) 
```

### 连接 redux devtools

我们将使用包`mst-middlewares`中的`connectReduxDevtools`中间件，它将状态树连接到 redux devtools(更多信息和配置选项可在[文档](https://github.com/mobxjs/mobx-state-tree/tree/master/packages/mst-middlewares#connectreduxdevtools)中获得)。为了设置连接，我们将使用一个监控工具 [`remotedev`](https://github.com/zalmoxisus/remotedev) 。首先安装软件包:

```
yarn add --dev remotedev mst-middlewares 
```

并在商店创建后添加以下代码:

```
import { createStore } from "../stores/createStore"
import { connectReduxDevtools } from "mst-middlewares"

const rootStore = createStore()

connectReduxDevtools(require("remotedev"), rootStore) 
```

## 连接 react 到 mobx

我最纠结的部分是如何将`react`连接到`mobx`并开始使用我的组件中的存储。这里的想法是，react 组件需要变得“有反应性”,并开始跟踪来自商店的可观察数据。

### 为什么不是 mobx-react

实现这一点最常见的方法是使用 [mobx-react](https://github.com/mobxjs/mobx-react) ，它提供了`observer`和`inject`功能，其中`observer`包裹着组件，使它们对变化做出反应并重新渲染，而`inject`只是将存储注入组件。但是，我不推荐使用这个库，因为:

*   当使用`observer`时，组件失去了使用钩子的能力，因为它被转换成了一个类，更多关于这个[在这里](https://github.com/mobxjs/mobx-react/issues/594)。在[最佳实践](https://mobx.js.org/best/pitfalls.html)中，文档建议在尽可能多的组件周围使用`observer`，这意味着钩子几乎不能在任何地方使用，
*   `inject`函数相当复杂，不能很好地与 typescript 一起工作(参见 [github 问题](https://github.com/mobxjs/mobx-react/issues/256#issuecomment-433587230))，要求所有的商店都标记为可选的，然后使用`!`来表示它们实际存在。

### mobx-react-lite 来救援了

幸好还有另外一个库， [`mobx-react-lite`](https://github.com/mobxjs/mobx-react-lite) ，是用钩子构建的，提供了`observer`包装器。值得一提的是，`observer`不支持类，但是有一个专用的组件`Observer`，可以在类组件 render 中包裹`jsx`的部分。

很容易与这个库混淆，因为它提供了许多钩子，如`useObservable`、`useComputed`等。根据[的文件](https://github.com/mobxjs/mobx-react-lite#notice-of-deprecation)，它们将被弃用。相反，这里有一个[推荐的方式](https://github.com/mobxjs/mobx-react/issues/256#issuecomment-433587230)，我们将遵循:

*   使用`react context` provider 传递商店，
*   使用带有选择器的`useContext`钩子访问商店，或者基于`useContext`钩子用自定义的`useInject`钩子注入必要的商店，
*   用来自`mobx-react-lite`的`observer`包装组件以订阅更改。

所以我们来装库:

```
yarn add mobx-react-lite 
```

### 上下文提供者传递存储

首先，让我们创建上下文`StoreContext`，它稍后将接收根存储作为它的`value`，并导出提供者和一个用于访问上下文值的自定义钩子:

```
const StoreContext = createContext<RootStoreModel>({} as RootStoreModel)

export const useStore = () => useContext(StoreContext)
export const StoreProvider = StoreContext.Provider 
```

然后用`createStore`创建根存储，并将其发送到`StoreProvider`中，我们包装`App` :

```
import { StoreProvider } from "./StoreProvider"
import { createStore } from "../stores/createStore"

const rootStore = createStore()

const Root: React.FunctionComponent<{}> = () => (
  <StoreProvider value={rootStore}>
    <App />
  </StoreProvider>
) 
```

### 自定义挂钩注入店铺

可以使用`useStore`钩子直接访问根存储并从中获取必要的数据，就像这样:

```
const { pollDraft } = useStore() 
```

我还实现了一个`useInject`钩子，它接收一个映射函数并返回一个映射对象，类似于在`redux`中用`mapStateToProps`完成的方式。这个钩子有点类似于带有映射器功能的[自定义注入](https://github.com/mobxjs/mobx-react#customizing-inject)的想法，但是带有钩子。因此，如果你有一个更复杂的应用程序，在你的商店里有很多东西，你可能只想得到你想要的东西，而不关心其他的。

最简单的形式是，`useInject` hook 可能是这样的:

```
export type MapStore<T> = (store: RootStoreModel) => T

const useInject = <T>(mapStore: MapStore<T>) => {
  const store = useStore()
  return mapStore(store)
} 
```

然后，`PollDraft`组件将使用`useInject`从根商店
访问`pollDraft`商店

```
import { observer } from "mobx-react-lite"
import { RootStoreModel } from "../stores/RootStore"
import useInject from "../hooks/useInject"

const mapStore = (rootStore: RootStoreModel) => ({ pollDraft: rootStore.pollDraft })

const PollDraft: React.FunctionComponent<{}> = observer(() => {
  const { pollDraft } = useInject(mapStore)

  return (
    <div>
      <h1>Create a new poll</h1>
      <input
        value={pollDraft.question}
        onChange={e => pollDraft.setQuestion(e.target.value)}
      />
      <button onClick={pollDraft.publish}>Publish</button>
    </div>
  )
}) 
```

如果`mapStore`功能更复杂，涉及到合并来自几个商店的数据和动作，这尤其有用。

* * *

在这一点上，我觉得我已经涵盖了基础知识，并创建了一个设置，我可以继续在其上构建，或者将它用作具有类似堆栈的项目的样板。源代码可以在我的 [github](https://github.com/MargaretKrutikova/mst-react-ts-guide) 上找到。

我希望这个演练是有用的，并且您找到了对您的项目有帮助的东西。我很乐意听到您对您认为有帮助的方面的反馈，或者与`typescript`的`mobx-state-tree`和`react`分享您自己的经验！