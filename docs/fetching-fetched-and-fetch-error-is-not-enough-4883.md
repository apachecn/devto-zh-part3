# 抓取，抓取，抓取错误不够。

> 原文：<https://dev.to/jsmanifest/fetching-fetched-and-fetch-error-is-not-enough-4883>

当我们读到 HTTP 请求时，我们经常会看到常见的*获取*、*获取*或*获取错误*状态。然后应该更新用户界面以反映该更新。这三种状态描述了执行任何 CRUD(创建、读取、更新、删除)操作的最重要的实现。

作为开发人员，我们有责任让用户参与到我们的界面中，并帮助他们获得最佳体验。我们认为用户是我们的盟友，不想要的虫子是我们的敌人。

例如，当检索数据时，我们希望让用户*知道*我们正在试图检索数据，而他们正在等待数据。当数据被检索时，我们应该显示数据。当错误发生时，无论错误对象中的原因是什么，都无法检索数据，我们必须让用户知道存在错误，并利用在错误中检索到的数据。我们最不想做的事情就是让他们挂着——当然，除非我们想让他们离开，永远不再回来。

*但这还不够*。

还有第四种状态，它不仅属于获取状态，也属于任何类型的操作，尤其是 CRUD 操作。

[![thinking-fetching-fourth-state-in-javascript](img/72966b5a2e3f2ab6ae88018473fbed2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9OGmn7Ax--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/fetching-fetched-fetcherror-is-not-enough/thinking.jpg)

## 超时

在我工作的公司，我们做很多 HTTP 请求。我们遇到的一个最大的问题是，在随机的网页中有随机的*冻结的*加载微调器，这些微调器最终被卡在这个阶段，直到用户刷新页面。然而，这并不总是发生。但是我的老板*真的不喜欢冷冻装载纺车。这是非常糟糕的用户体验。我不怪他，因为每个受到这个问题影响的用户都被晾在一边，被迫做一些*与我们希望他们做的*完全相反的事情。*

你能猜出它是什么吗？是的，你猜对了。他们点击后退按钮，去别的地方。他们关闭浏览器，让自己忙于其他事情。这样的例子不胜枚举。或者可能发生的最糟糕的事情...他们点击后退按钮，决定使用竞争对手的网站。我们刚刚失去了一位潜在的重要客户。糟糕的用户体验是隐性的金钱损失，不幸的是:(。

*你需要一个超时状态*。当服务器没有响应，或者由于某种原因，获取状态被分派，而调用在发送请求之前被阻塞(这种情况时有发生)，您附加了*获取*状态的加载微调器被冻结。它不再是一个临时的加载微调器，用来发出数据即将到来的信号。现在它是一个无限运行的负载旋转器，整个世界~~永远不会~~结束。现在，您已经将处理 bug 的责任交给了用户。作为一个开发者，你失败了。请再试一次。

那么我们如何在 React 中实现这一点呢？

一些库，如 [axios](https://github.com/axios/axios) 提供了一个*超时*选项。但是你不应该指望这是 100%准确的。我在我工作的公司的经验告诉我，这是不够的，我们不应该严重依赖它。

我们将继续进行更新/更新/更新错误实现，而不是进行通常的提取/提取/提取错误实现，因为我们在 JavaScript 中的每个角落都能听到“提取”。

在本教程中，我们将定制一个 [react hook](https://reactjs.org/docs/hooks-intro.html) ，它将提供一个方法`updateUser`来调用更新处理程序，并在调用 API 时在其中分派一些动作。它还将设置一个临时超时功能，在 ___ 秒后调用。

这个钩子将在几个州注册。这些状态连同更新处理程序将被传递给调用者。我们将从钩子实现开始，然后应用超时部分。

让我们从一个基本组件`App.js`开始，然后一步步往上走:

> App.js

```
import React from 'react'
import './App.css'

const App = (props) => {
  return <div>Update Timeout</div>
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

现在继续，从整个“更新用户”实现开始，我们将创建一个名为`useUpdateUser`的钩子。这个钩子的要点是对一个用户的*邮箱*或者*密码*进行更新操作。

> 多次更新超时. js

```
import axios from 'axios'

const useUpdateUser = () => {
  const updateUser = async (userId, params) => {
    try {
      if (!userId) {
        throw new Error('userId is undefined')
      } else if (!params) {
        throw new Error('params is undefined')
      }
      const url = `https://someapi.com/v1/account/${userId}/`
      const response = await axios.put(url, params)
      const updatedUser = response.data
      return updatedUser
    } catch (error) {
      throw error
    }
  }

  return {
    updateUser,
  }
}

export default useUpdateUser 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将使用 [*useReducer*](https://reactjs.org/docs/hooks-reference.html#usereducer) 来定义状态。我个人在几乎所有使用某种状态的钩子上都使用 useReducer(即使只有一种状态-_-)。

```
const initialState = {
  updating: false,
  updated: false,
  updateError: null,
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们定义了三个必要的状态来使一个应用程序正常运行。在 JavaScript 社区中，我们经常被告知，当请求中没有错误时，可以向 error 参数传递 null,这样调用者就会知道数据已被顺利检索到。因此，我们在这里对*更新错误*使用了相同的标准，因为它在这里也工作得很好。

现在我们需要定义一个缩减器来将更改应用于并发状态更新。减速器应反映初始状态:

```
import { useReducer } from 'react' 
```

Enter fullscreen mode Exit fullscreen mode

然后在 *initialState* 实现之后，我们将定义缩减器:

```
const reducer = (state, action) => {
  switch (action.type) {
    case 'updating':
      return { ...initialState, updating: true }
    case 'updated':
      return { ...initialState, updated: true }
    case 'set-error':
      return { ...initialState, updateError: action.error }
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到*初始状态*在每个开关情况下被扩展，而不是扩展通常的*状态*。这是为什么呢？

这有效地做了与你通常用扩展*状态*编写的*相同的事情*，只是现在我们不必编写所有的样板代码。为了避免不必要的错误和代码大小，我们希望整个实现尽可能简单*。当*更新*切换到*真*时，UI 应该被设置回原来的状态，只有*关心*更新*部分。当更新已经完成并且用户简档已经被更新时，UI 应该被设置回其原始状态，并且*仅*关心用户简档是否被更新(状态的*更新*部分)。这同样适用于*更新错误*。**

 *否则我们会这样写:

```
const reducer = (state, action) => {
  switch (action.type) {
    case 'updating':
      return { ...state, updated: false, updating: true }
    case 'updated':
      return { ...state, updated: true, updating: false, updateError: null }
    case 'set-error':
      return {
        ...state,
        updated: false,
        updating: false,
        updateError: action.error,
      }
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你喜欢哪个版本？我不知道你怎么想，但我更喜欢初始状态版本！(有人会说，使用 initialState 版本会剥夺我们状态更新的所有能力和灵活性。我完全同意，但这里的各州都在完成同样的目标)。

我们现在要做的下一件事是用 *useReducer* 将我们的实现附加到我们的`useUpdateTimeout`钩子上:

> 多次更新超时. js

```
const useUpdateUser = () => {
  const [state, dispatch] = useReducer(reducer, initialState)

  const updateUser = async (userId, params) => {
    try {
      if (!userId) {
        throw new Error('userId is undefined')
      } else if (!params) {
        throw new Error('params is undefined')
      }
      const url = `https://someapi.com/v1/account/${userId}/`
      const response = await axios.put(url, params)
      const updatedUser = response.data
      return updatedUser
    } catch (error) {
      throw error
    }
  }

  return {
    updateUser,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还想通过在 return 语句中传播这些有用的实用程序来为调用者提供这些实用程序，以便他们在状态改变时实际更新组件:

```
return {
  ...state,
  updateUser,
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们现在有这样的东西:

> 多次更新超时. js

```
import { useReducer } from 'react'
import axios from 'axios'

const initialState = {
  updating: false,
  updated: false,
  updateError: null,
}

const reducer = (state, action) => {
  switch (action.type) {
    case 'updating':
      return { ...initialState, updating: true }
    case 'updated':
      return { ...initialState, updated: true }
    case 'set-error':
      return { ...initialState, updateError: action.error }
    default:
      return state
  }
}

const useUpdateUser = () => {
  const [state, dispatch] = useReducer(reducer, initialState)

  const updateUser = async (userId, params) => {
    try {
      if (!userId) {
        throw new Error('userId is undefined')
      } else if (!params) {
        throw new Error('params is undefined')
      }
      const url = `https://someapi.com/v1/account/${userId}/`
      const response = await axios.put(url, params)
      const updatedUser = response.data
      return updatedUser
    } catch (error) {
      throw error
    }
  }

  return {
    ...state,
    updateUser,
  }
}

export default useUpdateUser 
```

Enter fullscreen mode Exit fullscreen mode

当我们让应用程序调用 *updateUser* 时，最好也让它在处理组件更新的过程中分派一些动作:

```
const updateUser = async (userId, params) => {
  try {
    dispatch({ type: 'updating' })
    if (!userId) {
      throw new Error('userId is undefined')
    } else if (!params) {
      throw new Error('params is undefined')
    }
    const url = `https://someapi.com/v1/api/user/${userId}/`
    const response = await axios.put(url, params)
    const updatedUser = response.data
    dispatch({ type: 'updated' })
    return updatedUser
  } catch (error) {
    dispatch({ type: 'set-error', error })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

UI 应该根据当时调度的操作类型而变化。

应用程序现在应该运行得很好，我们可以就此打住。然而，这篇文章要实现一个 *timedOut* 状态，所以我们接下来要实现它。

首先，我们应该考虑 JavaScript 已经为我们提供的 *setTimeout* 函数。这将有助于超时请求的发生，因为它可以用来调度 UI 组件可以监听的*超时*动作。

当*超时*动作被调度时，UI 应该立即丢弃它正在做的事情，并在它们的空间中显示操作超时。通过这种方式，用户将会知道他们的互联网出了问题或者服务器出了问题。您可以选择提供一个*重试*按钮来重试请求。我将制作另一个教程来实现它，所以如果你正在寻找一些指导，请耐心等待！

无论是谁，我们要声明的第一件事是 *where* 以附加 setTimeout 引用。

为此，我们将从 react 导入 *[useRef](https://reactjs.org/docs/hooks-reference.html#useref)* 并将其附加到*上。useEffect 块内的当前*属性:

```
import { useReducer, useRef } from 'react' 
```

Enter fullscreen mode Exit fullscreen mode

将其放入挂钩内:

```
const [state, dispatch] = useReducer(reducer, initialState)
const timeoutRef = useRef(null) 
```

Enter fullscreen mode Exit fullscreen mode

现在，在 *updateUser* 方法中，我们在这里声明 *setTimeout* 函数，开始倒计时，如果计时器到达其生命的终点
，则发送一个*超时*动作

```
const updateUser = async (userId, params) => {
  try {
    dispatch({ type: 'updating' })
    if (!userId) {
      throw new Error('userId is undefined')
    } else if (!params) {
      throw new Error('params is undefined')
    }
    if (timeoutRef.current) {
      clearTimeout(timeoutRef.current)
    }
    timeoutRef.current = setTimeout(() => {
      dispatch({ type: 'timed-out' })
    }, 30000)
    const url = `https://someapi.com/v1/api/user/${userId}/`
    const response = await axios.put(url, params)
    clearTimeout(timeoutRef.current)
    const updatedUser = response.data
    dispatch({ type: 'updated' })
    return updatedUser
  } catch (error) {
    clearTimeout(timeoutRef.current)
    dispatch({ type: 'set-error', error })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最终输出:

```
import { useReducer, useRef } from 'react'
import axios from 'axios'

const initialState = {
  updating: false,
  updated: false,
  updateError: null,
}

const reducer = (state, action) => {
  switch (action.type) {
    case 'updating':
      return { ...initialState, updating: true }
    case 'updated':
      return { ...initialState, updated: true }
    case 'set-error':
      return { ...initialState, updateError: action.error }
    case 'timed-out':
      return { ...initialState, timedOut: true }
    default:
      return state
  }
}

const useUpdateUser = () => {
  const [state, dispatch] = useReducer(reducer, initialState)
  const timeoutRef = useRef(null)

  const updateUser = async (userId, params) => {
    try {
      dispatch({ type: 'updating' })
      if (!userId) {
        throw new Error('userId is undefined')
      } else if (!params) {
        throw new Error('params is undefined')
      }
      if (timeoutRef.current) {
        clearTimeout(timeoutRef.current)
      }
      timeoutRef.current = setTimeout(() => {
        dispatch({ type: 'timed-out' })
      }, 30000)
      const url = `https://someapi.com/v1/api/user/${userId}/`
      const response = await axios.put(url, params)
      clearTimeout(timeoutRef.current)
      const updatedUser = response.data
      dispatch({ type: 'updated' })
      return updatedUser
    } catch (error) {
      clearTimeout(timeoutRef.current)
      dispatch({ type: 'set-error', error })
    }
  }

  return {
    ...state,
    updateUser,
  }
}

export default useUpdateUser 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，这实际上看起来像是一个完成的实现！然而，我喜欢通过让调用者提供一个自定义的超时来为钩子提供一点定制，以使它更加灵活:

```
const useUpdateUser = ({ timeout = 30000 }) => {
  const [state, dispatch] = useReducer(reducer, initialState)
  const timeoutRef = useRef(null)

  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

```
timeoutRef.current = setTimeout(() => {
  dispatch({ type: 'timed-out' })
}, timeout) 
```

Enter fullscreen mode Exit fullscreen mode

对于 *updateUser* 将要发生的事情是，它首先调度一个*更新*动作。UI 组件应该显示某种“待定”表示，这样用户就会对他们的配置文件被更新感到兴奋。如果这个方法被意外地调用了两次*，我们就在 *setTimeout* 行的上方有一个早期的*clear time out(time out ref . current)*，这样它就可以删除刚刚设置的前一个。*

 *其后的一行是 *setTimeout* 行。这是整篇文章中最重要的部分，因为没有它就不会有超时功能！:)

一旦 *await* 调用成功，我们就知道用户的档案更新成功了。一旦成功响应到达，我们就知道不再需要 *setTimeout* 语句，所以我们用*clear time out(time out ref . current)*删除它。最后，在执行结束时，我们调度一个 *updated* 动作，这样成功的概要文件更新就可以反映在界面上。

如果在更新过程中出现任何错误，还会运行*clear time out(time out ref . current)*。原因是因为我们实际上收到了来自请求的一些响应，超时不再与代码前进的路径相关，因为现在我们只关心发生的错误。

## 用户界面

这是钩子在真实场景中实现的一种方式:

```
import React, { useState } from 'react'
import './App.css'
import useUpdateUser from './useUpdateUser'

const App = (props) => {
  const {
    updating,
    updated,
    updateError,
    timedOut,
    updateUser,
  } = useUpdateUser({
    timeout: 12000,
  })

  const [email, setEmail] = useState('')
  const [password, setPassword] = useState('')

  const onSubmit = (e) => {
    e.preventDefault()
    const params = { email, password }
    updateUser('my-user-id123', params)
  }

  const isInitial = !updating && !updated && !updateError && !timedOut

  const errMsg =
    updateError &&
    (updateError.message || 'An error occurred. Please try again later')

  return (
    <div className='container'>
      <h2>
        {isInitial && 'Update your email or password below'}
        {updating && 'Updating your profile...'}
        {updated && 'Your profile has been updated'}
        {errMsg && <span className='error-txt'>{errMsg}</span>}
        {timedOut &&
          'We did not receive a response from the server. Please try again later'}
      </h2>
      <form onSubmit={onSubmit}>
        <div>
          <input
            type='text'
            placeholder='Email'
            name='email'
            value={email}
            onChange={(e) => setEmail(e.target.value)}
          />
        </div>
        <div>
          <input
            type='text'
            placeholder='Password'
            name='password'
            value={password}
            onChange={(e) => setPassword(e.target.value)}
          />
        </div>
        <div>
          <button type='submit'>Submit</button>
        </div>
      </form>
    </div>
  )
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

以下是最漂亮的实现截图:

### 初始界面:

[![initial-javascript](img/15382bd51ae2798ad69b28753448fb41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tmkg_fcS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/fetching-fetched-fetcherror-is-not-enough/1.JPG)

### 更新

[![updating-javascript-fetching](img/16cd4e85bffff4d997c81c38ada6c296.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XEAx-qwq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/fetching-fetched-fetcherror-is-not-enough/3.JPG)

### 更新

[![updated-javascript](img/902bf1f277d1ac3d7e858b7a28b826ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q1vPOLtt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/fetching-fetched-fetcherror-is-not-enough/2.JPG)

### 更新错误

[![error-javascript](img/abc5dab28fea6a5fa36cbb8b95c0c05a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZZLWkrqz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/fetching-fetched-fetcherror-is-not-enough/4.JPG)

### 更新超时

[![timeout-javascript](img/c42d4342163ccbd2e362785957af46f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wBsZmmwx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/fetching-fetched-fetcherror-is-not-enough/5.JPG)

## 结论

我们做到了！下次请继续关注另一个教程。另外，你可以在[https://jsmanifest.com](https://jsmanifest.com)订阅我的时事通讯，把我的更新直接发到你的收件箱里。他们是免费的*。***