# 使用 Redux 第 2 部分使 WebSocket 与 React 中的用户互联网连接同步

> 原文：<https://dev.to/jsmanifest/making-websocket-in-sync-with-user-internet-connectivity-in-react-using-redux-part-2-2bc2>

在本系列的第一部分中，我们用 redux 配置了一个 react 应用程序，并为互联网状态更新定义了状态结构以及调用更新的动作。我们创建了一个`useInternet`钩子来注册必要的事件处理程序，以允许应用程序调用相应的动作来改变和更新状态。

在本教程中，我们将通过实现应用程序的 WebSocket 功能来进一步增强应用程序。我们将确保当用户的互联网连接发生变化时，websocket 客户端将保持同步并做出适当的响应。

此外，我们将在 websocket 客户端意外关闭后添加一些额外的 UX。当 websocket 客户端意外关闭时，我们会让它自己复活。

*注*:本教程要求你了解一点关于 [websocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) API 的知识。

*注意#2* :如果你想下载本教程的源代码，你可以从[库](https://github.com/jsmanifest/ws-online-sync)中克隆它。

## 创建减速器

我们将从创建 websocket 状态更新的 reducers 开始。如果你还记得本教程的第一部分，我们编写了这样的代码:

```
import { INTERNET_ONLINE, INTERNET_OFFLINE } from '../actions'

const initialState = {
  internet: {
    isOnline: true,
  },
  ws: {
    connecting: false,
    opened: false,
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

查看`ws`状态片段，我们需要一个组件来监听`ws.opened`，当我们的 websocket 客户端打开或关闭时，这个组件会改变。

我们将首先创建一个定制的`useWebsocket`钩子，并从 redux 导入`useSelector`函数来监听这些更新:

> src/hooks/useWebsocket.js

```
import { useSelector } from 'react-redux'

const useWebsocket = ({ isOnline }) => {
  const opened = useSelector((state) => state.app.ws.opened)

  return {
    opened,
  }
}

export default useWebsocket 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将创建一个 UI 组件，它将接收`opened`作为道具:

> src/components/websocketconnection . js

```
import React from 'react'
import './styles.css'

const StatusMessage = ({ opened }) => (
  <h5>Your websocket is {opened ? 'opened' : 'not opened'}</h5>
)

const BodyContent = ({ opened }) => (
  <div>
    <p>
      {opened && 'Now go do stuff and have an amazing day!'}
      {!opened &&
        "You can't do anything right now. Make yourself a cup of coffee in the mean time."}
    </p>
  </div>
)

const WebsocketConnection = ({ opened }) => {
  return (
    <div className='wsc-container'>
      <div>
        <StatusMessage opened={opened} />
        <BodyContent opened={opened} />
      </div>
    </div>
  )
}

export default WebsocketConnection 
```

Enter fullscreen mode Exit fullscreen mode

使用上一篇教程中的`App.js`组件，我们将使用它来导入我们刚刚创建的`useWebsocket`钩子，这样我们就可以获取`opened`状态并将其传递给我们刚刚创建的 UI 组件:

> src/App.js

```
import React, { useEffect } from 'react'
import useInternet from './hooks/useInternet'
import useWebsocket from './hooks/useWebsocket'
import './App.css'
import WebsocketConnection from './components/WebsocketConnection'

const App = () => {
  const { isOnline } = useInternet()
  const { opened } = useWebsocket({ isOnline })

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
      <WebsocketConnection opened={opened} />
    </div>
  )
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

我接着应用了一些快速的 CSS 样式，让它看起来有点吸引人。如果您也想使用它们，我在这里提供了它们:

> src/components/styles.css

```
div.wsc-container {
  padding: 35px;
  display: flex;
  align-items: center;
  justify-content: center;
}

div.wsc-container > div:first-child {
  text-align: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们现在所拥有的:

[![your websocket is not opened. make yourself a cup of coffee in the mean time](img/f2e3ab10a1df74967a6455b813a791ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rHegePb9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/making-websocket-in-sync-with-internet-in-react-part-two/1.jpg)

目前，当 websocket 客户端打开时，除了屏幕上显示的内容之外，我们的组件不会做任何事情。那是因为我们还没有为 reducers 应用 action creators。

首先我们将为动作创建者创建三个常量:

```
export const WS_CONNECTING = 'WS_CONNECTING'
export const WS_OPENED = 'WS_OPENED'
export const WS_CLOSED = 'WS_CLOSED' 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将需要创建三个动作创建者，以便 reducers 能够与他们通信:

> src/actions/index.js

```
export const INTERNET_ONLINE = 'INTERNET_ONLINE'
export const INTERNET_OFFLINE = 'INTERNET_OFFLINE'
export const WS_CONNECTING = 'WS_CONNECTING'
export const WS_OPENED = 'WS_OPENED'
export const WS_CLOSED = 'WS_CLOSED'

export const internetOnline = () => ({
  type: INTERNET_ONLINE,
})

export const internetOffline = () => ({
  type: INTERNET_OFFLINE,
})

export const wsConnecting = () => ({
  type: WS_CONNECTING,
})

export const wsOpened = () => ({
  type: WS_OPENED,
})

export const wsClosed = () => ({
  type: WS_CLOSED,
}) 
```

Enter fullscreen mode Exit fullscreen mode

完成这些设置后，我们现在可以转到 reducers 文件并导入这三个常量:

> src/reducers/appReducers.js

```
import {
  INTERNET_ONLINE,
  INTERNET_OFFLINE,
  WS_CONNECTING,
  WS_OPENED,
  WS_CLOSED,
} from '../actions'

const initialState = {
  internet: {
    isOnline: true,
  },
  ws: {
    connecting: false,
    opened: false,
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

它将继续在 switch 语句中定义三个常量，以便在被调用时计算下一个状态:

```
case WS_CONNECTING:
  return { ...state, ws: { ...state.ws, connecting: true } } 
```

Enter fullscreen mode Exit fullscreen mode

```
case WS_OPENED:
  return { ...state, ws: { ...state.ws, connecting: false, opened: true } } 
```

Enter fullscreen mode Exit fullscreen mode

```
case WS_CLOSED:
  return { ...state, ws: { ...state.ws, connecting: false, opened: false } } 
```

Enter fullscreen mode Exit fullscreen mode

```
const appReducer = (state = initialState, action) => {
  switch (action.type) {
    case INTERNET_ONLINE:
      return { ...state, internet: { ...state.internet, isOnline: true } }
    case INTERNET_OFFLINE:
      return { ...state, internet: { ...state.internet, isOnline: false } }
    case WS_CONNECTING:
      return { ...state, ws: { ...state.ws, connecting: true } }
    case WS_OPENED:
      return { ...state, ws: { ...state.ws, connecting: false, opened: true } }
    case WS_CLOSED:
      return { ...state, ws: { ...state.ws, connecting: false, opened: false } }
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

一切都好！到目前为止，我们已经将 UI 与 reducer 状态连接起来，然后我们创建了动作创建器来帮助将动作分派给 reducer。减速器将从那里开始计算下一个状态，以便组件可以更新。

我们现在需要做的是实例化一个 websocket 客户端实例。但是，它需要一个 websocket url 来连接。为了这个教程，我提供了一个我们可以使用的公共教程:

```
const wsUrl = 'wss://echo.websocket.org' 
```

Enter fullscreen mode Exit fullscreen mode

通过将 URL 传递给构造函数，您将能够创建并保持 websocket 连接打开。我们将继续创建一个`wsRef`，它将被分配一个`useRef`。这是我们将 websocket 客户端实例与。

> src/hooks/useWebsocket.js

```
import { useRef, useEffect } from 'react'
import { useSelector } from 'react-redux'

const wsUrl = 'wss://echo.websocket.org'

const useWebsocket = ({ isOnline }) => {
  const opened = useSelector((state) => state.app.ws.opened)

  const wsRef = useRef()

  // Initiates the websocket client on mount
  useEffect(() => {
    if (!wsRef.current) {
      wsRef.current = new WebSocket(wsUrl)
    }
  }, [])

  return {
    opened,
    ws: wsRef.current,
  }
}

export default useWebsocket 
```

Enter fullscreen mode Exit fullscreen mode

我们继续创建了一个`useEffect`，它将自动实例化并在`wsRef`上附加一个新的 websocket 客户端，这样我们就可以在初始加载时看到结果。

为了方便起见，我提供了一个实例化 websocket 客户端的方法和一个结束当前打开的 websocket 客户端的方法:

```
const initWebsocket = () => {
  if (wsRef.current) {
    wsRef.current.close()
  }
  wsRef.current = new WebSocket(wsUrl)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
const endWebsocket = () => {
  if (wsRef.current) {
    wsRef.current.close()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:

> src/hooks/useWebsocket.js

```
import { useRef, useEffect } from 'react'
import { useSelector } from 'react-redux'

const wsUrl = 'wss://echo.websocket.org'

const useWebsocket = ({ isOnline }) => {
  const opened = useSelector((state) => state.app.ws.opened)

  const wsRef = useRef()

  const initWebsocket = () => {
    if (wsRef.current) {
      wsRef.current.close()
    }
    wsRef.current = new WebSocket(wsUrl)
  }

  const endWebsocket = () => {
    if (wsRef.current) {
      wsRef.current.close()
    }
  }

  // Initiates the websocket client on mount
  useEffect(() => {
    if (!wsRef.current) {
      wsRef.current = new WebSocket(wsUrl)
    }
  }, [])

  return {
    ws: wsRef.current,
    opened,
    initWebsocket,
    endWebsocket,
  }
}

export default useWebsocket 
```

Enter fullscreen mode Exit fullscreen mode

我们将在 UI 中添加两个额外的按钮，这样我们就可以支持用户可以手动打开/关闭 websocket 客户端的功能(这个特性在本教程中并没有真正使用，但是很容易实现)。其中一个将用于在单击时启动新的 websocket 客户端实例，另一个将结束它:

> src/App.js

```
const { ws, opened, initWebsocket, endWebsocket } = useWebsocket({ isOnline }) 
```

Enter fullscreen mode Exit fullscreen mode

```
<div className='button-controls'>
  <button type='button' onClick={initWebsocket}>
    Initiate Websocket
  </button>
  <button type='button' onClick={endWebsocket}>
    End Websocket
  </button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

太好了！

但是等等。我们为组件创建了一种更新方式，但是它们需要一个更新的地点和时间。

我们将返回到我们的`initWebsocket`函数，并将一些事件监听器附加到`open`和`close`事件:

> src/hooks/useWebsocket.js

```
const initWebsocket = () => {
  if (wsRef.current) wsRef.current.close()
  wsRef.current = new WebSocket(wsUrl)
  wsRef.current.addEventListener('message', () => {})
  wsRef.current.addEventListener('open', () => {})
  wsRef.current.addEventListener('close', () => {})
  wsRef.current.addEventListener('error', () => {})
} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，websocket 连接可以有四个不同的侦听器:

| 听众 | 描述 |
| --- | --- |
| 无糖的 | 当 WebSocket 连接的 readyState 更改为 CLOSED 时调用 |
| onmessage | 当从服务器收到消息时调用 |
| 不要动 | 当 WebSocket 连接的 readyState 更改为 OPEN 时调用 |
| 不良事件 | 当 WebSocket 上发生错误时调用 |

```
 | 
```

Enter fullscreen mode Exit fullscreen mode

有了这些，我们现在可以期待附加一些处理程序:

```
const onMessage = (msg) => {
  console.log(msg)
}

const onOpen = () => {
  console.log('WS client opened')
}

const onClose = () => {
  console.log('WS client closed')
}

const onError = () => {
  console.log('WS client errored')
}

const initWebsocket = () => {
  if (wsRef.current) wsRef.current.close()
  wsRef.current = new WebSocket(wsUrl)
  wsRef.current.addEventListener('message', onMessage)
  wsRef.current.addEventListener('open', onOpen)
  wsRef.current.addEventListener('close', onClose)
  wsRef.current.addEventListener('error', onError)
} 
```

Enter fullscreen mode Exit fullscreen mode

目前我们的 *useEffect* 正在 *wsRef.current* 上附加一个新的 websocket 客户端实例，但是它现在缺少注册事件处理程序的实现。所以我们需要做一个快速更新，让它调用处理程序来进行注册:

> src/hooks/useWebsocket.js

```
// Initiates the websocket client on mount
useEffect(() => {
  if (!wsRef.current) initWebsocket()
}, [initWebsocket]) 
```

Enter fullscreen mode Exit fullscreen mode

此外，由于我们在启动器处理程序中注册了事件侦听器，因此我们还需要确保在客户端关闭时删除它们，以避免内存泄漏:

> src/hooks/useWebsocket.js

```
const endWebsocket = () => {
  if (wsRef.current) {
    wsRef.current.removeEventListener('message', onMessage)
    wsRef.current.removeEventListener('open', onOpen)
    wsRef.current.removeEventListener('close', onClose)
    wsRef.current.removeEventListener('error', onError)
    wsRef.current.close()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

从一开始，我们的目标就是让 *websocket 与用户的互联网连接*同步。看看到目前为止我们在代码中所得到的，我们现在有一个 API 设置来允许我们接近那个功能。

当用户的互联网离线时，我们的 *websocket `close`事件处理程序必须以某种方式调用*。

问题是，websocket 客户端与互联网连接不同步。我在 linux 和 windows 机器上进行了测试，当互联网关闭时，websocket 客户端的 *[readyState](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket/readyState)* 属性仍然会停留在 *1* (客户端的*打开*状态的值)。所以我们不能依赖它自己关闭。

要快速了解不同的*就绪状态*，您可以点击上面的链接或查看下表:

请记住，websocket 在其连接的整个生命周期中会经历四种不同的状态:

| 价值 | 状态 | 描述 |
| --- | --- | --- |
| Zero | 连接 | 套接字已创建。连接尚未打开。 |
| one | 打开 | 连接已打开，可以通信了。 |
| Two | 结束的 | 连接正在关闭过程中。 |
| three | 关闭的 | 连接已关闭或无法打开。 |

如果互联网离线，那么我们需要立即调用 websocket 客户端的 *close* 处理程序。当它恢复在线时，我们需要对 *open* 处理程序做同样的事情，否则 websocket 客户端将显示用户仍然连接，即使他的互联网断开连接。非常误导！我们应该解决这个问题。

回到我们之前创建的动作创建器，我们可以利用它们向我们的 reducers 发送信号:

> src/actions/index.js

```
export const wsConnecting = () => ({
  type: WS_CONNECTING,
})

export const wsOpened = () => ({
  type: WS_OPENED,
})

export const wsClosed = () => ({
  type: WS_CLOSED,
}) 
```

Enter fullscreen mode Exit fullscreen mode

这是我们减速器的最后更新:

> src/reducers/appReducers.js

```
import {
  INTERNET_ONLINE,
  INTERNET_OFFLINE,
  WS_CONNECTING,
  WS_OPENED,
  WS_CLOSED,
} from '../actions'

const initialState = {
  internet: {
    isOnline: true,
  },
  ws: {
    connecting: false,
    opened: false,
  },
}

const appReducer = (state = initialState, action) => {
  switch (action.type) {
    case INTERNET_ONLINE:
      return { ...state, internet: { ...state.internet, isOnline: true } }
    case INTERNET_OFFLINE:
      return { ...state, internet: { ...state.internet, isOnline: false } }
    case WS_CONNECTING:
      return { ...state, ws: { ...state.ws, connecting: true } }
    case WS_OPENED:
      return { ...state, ws: { ...state.ws, connecting: false, opened: true } }
    case WS_CLOSED:
      return { ...state, ws: { ...state.ws, connecting: false, opened: false } }
    default:
      return state
  }
}

export default appReducer 
```

Enter fullscreen mode Exit fullscreen mode

我们的 reducers 都和 action creators 有联系，现在他们应该在更新了。我们的下一步是更新 UI 组件。我们将修改 *useWebsocket* 钩子来调用一些处理程序，这样我们的 UI 组件就可以更新了——不用担心任何逻辑。这样做将使我们的代码在将来更容易阅读和维护。

在我们的钩子内，我们将创建另一个*使用效果*，它将在每次*的值改变时调用。当*在线*变为`false`时，我们将继续调度*关闭*动作。当它变为`true`时，我们将调度 *wsOpened* 动作。这样做会使附加到状态的所有组件根据更改进行相应的更新。*

> src/hooks/useWebsocket.js

```
// Responsible for updating redux when isOnline changes
useEffect(() => {
  if (isOnline && !opened) {
    dispatchAction(wsOpened())
  } else if (!isOnline && opened) {
    dispatchAction(wsClosed())
  }
}, [isOnline, dispatchAction, opened]) 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们还需要在 *endWebsocket* 函数中调度 *wsClosed* 动作，以帮助*useEffect*s 在用户的互联网连接发生变化时保持同步:

> src/hooks/useWebsocket.js

```
const endWebsocket = () => {
  if (wsRef.current) {
    wsRef.current.removeEventListener('message', onMessage)
    wsRef.current.removeEventListener('open', onOpen)
    wsRef.current.removeEventListener('close', onClose)
    wsRef.current.removeEventListener('error', onError)
    wsRef.current.close()
    if (opened) dispatchAction(wsClosed())
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在 redux 中的状态现在应该会更新，并尝试保持 websocket 客户端同步。然而，websocket 客户端不会自动关闭。我们需要通过调用 onClose 处理程序中的 *[关闭](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket/close)* 方法来*关闭*:

> src/hooks/useWebsocket.js

```
import { useDispatch, useSelector } from 'react-redux'
import { wsOpened, wsClosed } from '../actions' 
```

Enter fullscreen mode Exit fullscreen mode

```
const dispatchAction = useDispatch() 
```

Enter fullscreen mode Exit fullscreen mode

```
const onOpen = (e) => {
  console.log('WS client opened')
} 
```

Enter fullscreen mode Exit fullscreen mode

```
const onClose = (e) => {
  console.log('WS client closed')
  if (wsRef.current) {
    wsRef.current.close()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经为 websocket 客户端的 *open* 和 *close* 事件处理程序附加了动作创建器，我们现在就可以将钩子导入到组件中:

```
import React, { useEffect } from 'react'
import useInternet from './hooks/useInternet'
import useWebsocket from './hooks/useWebsocket'
import WebsocketConnection from './components/WebsocketConnection'
import './App.css'

const App = () => {
  const { isOnline } = useInternet()
  const { ws, opened, initWebsocket, endWebsocket } = useWebsocket({ isOnline })

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
      <WebsocketConnection opened={opened} />
      <div className='button-controls'>
        <button type='button' onClick={initWebsocket}>
          Initiate Websocket
        </button>
        <button type='button' onClick={endWebsocket}>
          End Websocket
        </button>
      </div>
    </div>
  )
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。尝试断开您的互联网并查看结果:

在线状态:

[![2](img/468b9c29c1565a9836e34092619e1b1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AmYHHS-P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/making-websocket-in-sync-with-internet-in-react-part-two/2.JPG)

接下来，我断开了互联网:

[![3](img/8cc42cd6b4b78b2d973ada099e47f867.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j0Kn35kS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/making-websocket-in-sync-with-internet-in-react-part-two/3.JPG)

连接回互联网:

[![4](img/28aea26405eb88b3c64b765fef0682ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--arK0OXZ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/making-websocket-in-sync-with-internet-in-react-part-two/4.JPG)

## 结论

这个系列到此结束！