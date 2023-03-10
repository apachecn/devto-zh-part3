# 使用 Redux 第 1 部分使 WebSocket 与 React 中的用户互联网连接同步

> 原文：<https://dev.to/jsmanifest/making-websocket-in-sync-with-user-internet-connectivity-in-react-using-redux-part-1-5cj0>

当我最近在一家初创公司负责实现 [web socket](https://developer.mozilla.org/en/docs/Web/API/WebSocket) 功能时，因为后端的变化，这并不是一次轻松的冒险。我不仅不熟悉 websockets，而且谷歌搜索也几乎没有为 react 应用程序中稳定的 web socket 架构提供任何解决方案。如果是这样的话，我在哪里可以找到教程呢？

良好的...最后，我没有使用符合我需要的教程。提供任何良好信息来源的文章通常都是过时的(2017 年及更早)，并且使用了现在被认为是不良做法的语法。JavaScript 是一个快速发展的社区，技术变化很快。我不相信任何 2018 年以前的教程。那只会让我更糟。

所以很幸运我找到了 [react-websocket](https://github.com/mehmetkose/react-websocket) ，好好看看源代码。它给了我一个从哪里开始的想法。

直到后来我才知道，我之前对应用程序的 websockets 实现与用户的互联网连接不同步，所以我的老板让我修复了它。看着 react-websocket，我开始意识到 websocket 客户端可以被实例化并附加到 react 组件实例，这是保持 websocket 连接与 UI 更新同步的良好开端。看到将一个特性直接实现到一个生产应用程序中，并将其发布到行业中是多么可怕，这是一个可怕的想法。所以我开始考虑这种方法所有可能的缺点，并意识到一个主要问题:如果组件被卸载了怎么办？websocket *关闭*它的连接。真扫兴。我正在实现这个特性的应用程序*严重依赖于打开的 websocket 连接的持久性。一旦用户的网络连接出现*任何*变化的迹象，我最好通知用户或者以某种方式立即*更新 UX*。*

我开始尝试将 websocket 客户端实例化并附加到浏览器的`window`对象上，但是效果并不好，因为 react 没有将自己更新到窗口事件。在 react 组件中，让用户的互联网与他们的 websocket 连接同步的所有本地状态管理是一场噩梦。在重新渲染和潜在的内存泄漏中有很多意想不到的无限循环。

我利用 react 的新特性 *context* 试图为所有子组件提供一个全局状态，但这不是一个好主意，因为它使 context 组件因来自 websocket 客户端的状态更新而变得臃肿，我必须优化它包装的所有子组件，以便仅在必要时重新呈现。但那完全没有必要。有更好的方法。

## Redux

我最终利用 redux 来处理状态更新。好处是巨大的:

1.  只有连接的子组件才需要担心更新
2.  状态是持久的*无处不在*
3.  使用 websocket 客户端连接更容易策划互联网在线/离线事件。简洁的语法使得代码更容易管理。
4.  那是*快*。

当我完成整个实现并将其推向生产时，我的老板再也没有提到 websocket 的问题。到现在已经一个多月了。

本文是使用 Redux 系列使 WebSocket 与 React 中的用户互联网连接同步的第 1 部分，共 2 部分。这不是一个鼓励你这是编码特性的*正确方式*的帖子，而只是一个我展示**在一个生产应用中为我和我的公司**工作的帖子。如果有更强大的方法或任何问题，欢迎在这篇文章下面发表评论！

我们将使用 [create-react-app](https://github.com/facebook/create-react-app) (CRA)来快速引导一个 react 应用，这样我们就可以开始代码实现了。

创建一个新的 CRA 项目，并将其命名为任何名称。我把它命名为 *ws-online-sync*

```
npx create-react-app ws-online-sync 
```

Enter fullscreen mode Exit fullscreen mode

进入目录:

```
cd ws-online-sync 
```

Enter fullscreen mode Exit fullscreen mode

安装 redux 和 react-redux(注意:我们的例子是使用 react-redux `v7.1.0-alpha.5`。你必须安装 react-redux@next 来使用这些例子，否则你将得到一个错误)

```
npm install --save redux react-redux@next 
```

Enter fullscreen mode Exit fullscreen mode

安装完依赖项后，我们可以继续清理 App.js(从主 index.js 文件导入和呈现的组件)，使其看起来更整洁一些:

```
import React from 'react'
import './App.css'

const App = () => {
  return (
    <div>
      <h1>Making Websocket in Sync with the User's Internet Connectivity</h1>
      <hr />
    </div>
  )
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

由于我们将使用 redux，我们必须做一些快速配置，以使它在应用程序中工作。

这是我们示例的目录结构:

[![javascript websocket in sync 1](img/51df662c853b2f2be658ef4c6791bf60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QwyCv-ym--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-1.amazonaws.com/jsmanifest/posts/making-websocket-in-sync-with-internet-in-react/1.JPG)

我们的组件需要用从 *react-redux* 包中导出的[提供者](https://react-redux.js.org/api/provider)进行包装。它将*商店*作为道具，并使其在用户客户端会话的整个生命周期中可用。

可以通过从 redux 导入 [createStore](https://redux.js.org/api/createstore) 来实例化和配置商店。

```
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import './index.css'
import App from './App'
import * as serviceWorker from './serviceWorker'
import rootReducer from './reducers'

const store = createStore(rootReducer)

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root'),
)

serviceWorker.unregister() 
```

Enter fullscreen mode Exit fullscreen mode

我们将 *rootReducer* 作为参数传递给了 *createStore* 方法。这是强制性的，因为它将包含我们的互联网和 websocket 状态的状态结构。

> reducers/index.js

```
import { combineReducers } from 'redux'
import app from './appReducers'

export default combineReducers({
  app,
}) 
```

Enter fullscreen mode Exit fullscreen mode

下面是它在目录结构中的样子:

[![directory structure websocket internet sync](img/ca2c048061cc0e4fbe12c9f1c2eef7e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WRsqsX4i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-1.amazonaws.com/jsmanifest/posts/making-websocket-in-sync-with-internet-in-react/2.JPG)

我们需要 rootReducer 在每次用户的互联网和 websocket 客户端连接改变时不断地返回下一个状态树。

redux [关于 reducer](https://redux.js.org/basics/reducers)的文档解释说“reducer 指定了应用程序的状态如何改变以响应发送到存储的动作”。

也就是说，我们现在需要定义发送到存储的动作，以便 reducers 进行更新。

使用动作[类型](https://redux.js.org/faq/actions#why-should-type-be-a-string-or-at-least-serializable-why-should-my-action-types-be-constants)的标准方法是使用常数，我喜欢这种标准方法，所以我需要这样定义动作的常数:

> actions/index.js

```
export const INTERNET_ONLINE = 'INTERNET_ONLINE'
export const INTERNET_OFFLINE = 'INTERNET_OFFLINE' 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以继续定义动作创建者:

> actions/index.js

```
export const INTERNET_ONLINE = 'INTERNET_ONLINE'
export const INTERNET_OFFLINE = 'INTERNET_OFFLINE'

export const internetOnline = () => ({
  type: INTERNET_ONLINE,
})

export const internetOffline = () => ({
  type: INTERNET_OFFLINE,
}) 
```

Enter fullscreen mode Exit fullscreen mode

reducer 将导入这些常量，以便在 switch 语句中使用它们:

> reducers/appReducers.js

```
import { INTERNET_ONLINE, INTERNET_OFFLINE } from '../actions'

const initialState = {
  internet: {
    isOnline: true,
  },
}

const appReducer = (state = initialState, action) => {
  switch (action.type) {
    case INTERNET_ONLINE:
      return { ...state, internet: { ...state.internet, isOnline: true } }
    case INTERNET_OFFLINE:
      return { ...state, internet: { ...state.internet, isOnline: false } }
    default:
      return state
  }
}

export default appReducer 
```

Enter fullscreen mode Exit fullscreen mode

太好了！我们现在已经在 redux 上连接了互联网在线/离线，并准备向下移动到组件。需要知道该状态更新的组件将简单地把自己与该状态片挂钩。

在接下来的例子中，我们将使用 React 16.8 中新增加的 react *[钩子](https://reactjs.org/docs/hooks-overview.html)* 特性。

我们将创建一个 *useInternet* 钩子，用在应用程序组件的顶部，这样我们就可以在需要时获得尽可能多的 UI 来读取它。

在 *src* 目录下创建一个 *hooks* 文件夹，并在里面创建一个 *useInternet.js* 文件。

[![hooks websocket internet javascript in sync](img/d34e56cfb919b410a5220e2ba42ce229.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ixlXTUZc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-1.amazonaws.com/jsmanifest/posts/making-websocket-in-sync-with-internet-in-react/3.JPG)

这个 useInternet 钩子将通过使用`window.addEventListener`向全局窗口对象注册一个`online`和`offline`事件。

这是任何支持离线的网络应用程序所需要的，根据我的经验，这对于让你的应用程序与用户的互联网连接保持同步非常有效和精确。当用户的互联网离线时，我们将在 redux 中调度一个动作，以便应用程序中的*任何*组件将根据他们的网络连接进行相应的更新。

> hooks/useInternet.js

```
import { useEffect } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { internetOnline, internetOffline } from '../actions'

const useInternet = () => {
  const dispatchAction = useDispatch()
  const isOnline = useSelector((state) => state.app.internet.isOnline)

  // Registers event listeners to dispatch online/offline statuses to redux
  useEffect(() => {
    const handleOnline = () => {
      dispatchAction(internetOnline())
    }

    const handleOffline = () => {
      dispatchAction(internetOffline())
    }

    window.addEventListener('online', handleOnline)
    window.addEventListener('offline', handleOffline)

    return function cleanup() {
      window.removeEventListener('online', handleOnline)
      window.removeEventListener('offline', handleOffline)
    }
  }, [dispatchAction])

  return {
    isOnline,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

简单、有效且强大:)

推荐使用`return function cleanup()`代码块，这样当组件卸载时[，它将避免应用程序中的内存泄漏](https://reactjs.org/docs/hooks-effect.html#example-using-hooks-1)。

这个定制钩子*应该*可以检测互联网连接的变化，但是我们可以通过提供第二个 useEffect 钩子和使用全局窗口对象的 [navigator.onLine](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorOnLine/onLine) 属性来进一步确保准确性。鉴于几乎所有浏览器都广泛支持，这是一个很容易的决定，有助于保持钩子对于生产应用程序来说更加健壮、准确和有用:

```
// Invokes the redux dispatchers when there is a change in the online status of the browser
useEffect(() => {
  if (window.navigator.onLine && !isOnline) {
    dispatchAction(internetOnline())
  } else if (!window.navigator.onLine && isOnline) {
    dispatchAction(internetOffline())
  }
}, [dispatchAction, isOnline]) 
```

Enter fullscreen mode Exit fullscreen mode

这里是使用互联网钩子的最后代码:

```
import { useEffect } from 'react'
import { useSelector, useDispatch } from 'react-redux'
import { internetOnline, internetOffline } from '../actions'

const useInternet = () => {
  const dispatchAction = useDispatch()
  const isOnline = useSelector((state) => state.app.internet.isOnline)

  // Registers event listeners to dispatch online/offline statuses to redux
  useEffect(() => {
    const handleOnline = () => {
      dispatchAction(internetOnline())
    }

    const handleOffline = () => {
      dispatchAction(internetOffline())
    }

    window.addEventListener('online', handleOnline)
    window.addEventListener('offline', handleOffline)

    return function cleanup() {
      window.removeEventListener('online', handleOnline)
      window.removeEventListener('offline', handleOffline)
    }
  }, [dispatchAction])

  // Invokes the redux dispatchers when there is a change in the online status of the browser
  useEffect(() => {
    if (window.navigator.onLine && !isOnline) {
      dispatchAction(internetOnline())
    } else if (!window.navigator.onLine && isOnline) {
      dispatchAction(internetOffline())
    }
  }, [dispatchAction, isOnline])

  return {
    isOnline,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试这有多准确，将这个钩子导入到 App.js 组件中，并提供一个 useEffect 来响应 internet 连接的变化，如下所示:

> App.js

```
import React, { useEffect } from 'react'
import useInternet from './hooks/useInternet'
import './App.css'

const App = () => {
  const { isOnline } = useInternet()

  useEffect(() => {
    console.log(
      `%cYou are ${isOnline ? 'online' : 'offline'}.`,
      `color:${isOnline ? 'green' : 'red'}`,
    )
  }, [isOnline])

  return (
    <div>
      <h1>Making Websocket in Sync with the User's Internet Connectivity</h1>
      <hr />
    </div>
  )
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

现在运行应用程序，在开发者工具中打开控制台，断开你的互联网。重新打开后，您的控制台应该会显示:

[![console messages websocket javascript in sync react app](img/c007cd0d766a557513a898bb441a6b49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DrB5u_Cr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-1.amazonaws.com/jsmanifest/posts/making-websocket-in-sync-with-internet-in-react/4.JPG)

## 总结——第一部分

第一部分到此结束！我们已经配置了一个 redux react 应用程序，并为*互联网*状态更新以及调用更新的动作定义了状态结构。我们还创建了一个`useInternet`钩子来注册事件处理程序，并允许应用程序调用相应的动作来进行状态更新。

在第二部分中，我们将继续在应用程序中实现 WebSocket 功能。我们将确保当用户的互联网连接发生变化时，websocket 将保持同步并做出相应的反应。我们将确保当用户的互联网离线时，WebSocket 客户端将恢复自身并“记住”应用程序的先前状态。我们还将确保 web socket 客户端在放弃之前会尝试重新连接 3 次。

请继续关注第 2 部分！