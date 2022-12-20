# 你可能不需要 Redux

> 原文：<https://dev.to/jaffparker/you-might-not-need-redux-k4e>

Redux 是一个非常棒的状态管理库。虽然非常简约，但它提供了在 React 项目中容易被忽略的结构和顺序。这就是为什么我会在每次启动 React 项目时自动安装它。它将是我的缓存、我的应用程序状态管理器~~和最好的朋友~~。

然后我发现了 Apollo 客户端，它可以在没有太多开销的情况下为您管理缓存。在我的开发经验中，那时 Redux 的角色开始*减少*(双关语)。基本上，我只会将它用于认证和网络/服务器状态。

然后 React 发布了钩子，包括 [`useReducer`](https://reactjs.org/docs/hooks-reference.html#usereducer) 一个，它的用法让我想起了很多 Redux...在这一点上，我开始重新思考我是否真的需要在我的代码中增加一个额外的依赖来管理很少的东西。

在这篇文章中，我将描述我离开 Redux 的原因，并介绍迁移的过程。希望它能帮助你们中的一些人对自己最喜欢的图书馆保持开放的心态，并意识到什么时候该放弃它们了:)

## 为什么离开 Redux？

有几个原因促使我探索用 hooks API 替换 Redux。

首先，我为 4 个动作和 2 个状态安装了一个额外的 NPM 包。这似乎非常过分，增加了项目的复杂性。此外，React 提供了基本应用状态管理所需的一切。如果不使用，就是浪费代码。

其次，我开始对输入连接的组件变得如此复杂感到恼火...有时我不得不编写大量额外的代码，只是为了知道用户是否通过了身份验证。

第三，和许多人一样，我立刻爱上了 React 钩子以及它们与功能组件的紧密联系(这是我在前端开发中仅次于钩子本身的第二喜欢的东西)。

## 我是怎么离开 Redux 而不摔坏任何东西的？

我的 Redux 商店有 2 个减速器组合:`auth`和`appStatus`。首先，让我们看看我是如何迁移`auth`的。

`auth`状态很简单:

```
interface AuthState {
  isSignedIn: boolean
  token: string
  user: User
} 
```

随之而来的是两个动作:`signIn`和`signOut`。

我注意到的第一件事是 React 的`useReducer`钩子和 Redux 有相同的 reducer 签名。所以最棒的是你可以完全重复使用你的减速器！然而，我不能把 reducer 放在上下文中。我需要能够从嵌套组件中*更新它，所以我遵循了官方文档中名为[从嵌套组件](https://reactjs.org/docs/context.html#updating-context-from-a-nested-component)中更新上下文的教程(真巧？？).这样这个代码就诞生了:* 

```
// contexts/AuthContext.ts

export const AuthContext = createContext<AuthContextState>({
  isSignedIn: false,
})
export const AuthProvider = AuthContext.Provider 
```

```
// components/AuthContextContainer.tsx

import {
  auth,
  signIn as signInAction,
  signOut as SignOutAction,
} from '../reducers/auth.ts'

export const AuthContextContainer: FC = ({ children }) => {
  const [state, dispatch] = useReducer(auth)

  const signIn = useCallback((user: User, token: string) => {
    dispatch(signInAction(user, token))
  }, [])
  const signOut = useCallback(() => {
    dispatch(signOutAction())
  }, [])

  return (
    <AuthProvider value={{ ...state, signOut, signIn }}>
      {children}
    </AuthProvider>
  )
} 
```

嘭！这是 Redux auth 商店。现在要在我的组件中使用它，而不是使用它们，我只需要这样做:

```
export const SignInContainer: FC = () => {
  const { signIn } = useContext(AuthContext)

  const onSubmit = async ({email, password}: SignInFormValues): void => {
    const { token, user } = await getTokenAndUserFromSomewhere(email, password)
    signIn(user, token)
  }

  return (
    // ... put the form here
  )
} 
```

现在我可以登录应用程序并四处浏览了！当我重新加载页面时会发生什么？嗯，你可能已经猜到了，应用程序不知道我曾经登录过，因为根本没有状态持久性...为了解决这个问题，我修改了`AuthContextContainer`来保存每次修改时的状态到`localStorage`中:

```
export const AuthContextContainer: FC = ({ children }) => {

  // LOOK HERE
  const initialState = localStorage.getItem('authState')

  const [state, dispatch] = useReducer(
    auth,

    // AND HERE
    initialState ? JSON.parse(initialState) : { isSignedIn: false },
  )

  const signIn = useCallback((user: User, token: string) => {
    dispatch(signInAction(user, token))
  }, [])
  const signOut = useCallback(() => {
    dispatch(signOutAction())
  }, [])

  // AND HERE
  useEffect(() => {
    localStorage.setItem('authState', JSON.stringify(state))
  }, [state])

  return (
    <AuthProvider value={{ ...state, signOut, signIn }}>
      {children}
    </AuthProvider>
  )
} 
```

现在`useReducer`钩子得到了一个初始状态，并且它在每次使用`useEffect`钩子改变时都保持不变！我不知道你怎么想，但我觉得这很棒。一个组件和一个上下文完全可以做整个库曾经做过的事情。

现在我将向你展示我对`appStatus`状态做了什么。`appStatus`只有一项工作:观察网络可用性，并存储我们是在线还是离线。下面是它是如何做到的:

```
export const watchNetworkStatus = () => (dispatch: Dispatch) => {
  window.addEventListener('offline', () =>
    dispatch(networkStatusChanged(false)),
  )
  window.addEventListener('online', () => dispatch(networkStatusChanged(true)))
}

export interface AppStatusState {
  isOnline: boolean
}
const defaultState: AppStatusState = {
  isOnline: navigator.onLine,
}

export const appStatus = (
  state: AppStatusState = defaultState,
  action: AppStatusAction,
): AppStatusState => {
  switch (action.type) {
    case AppStatusActionTypes.NetworkStatusChanged:
      return {
        ...state,
        isOnline: action.payload.isOnline,
      }

    default:
      return state
  }
} 
```

你可以看到，为了观察网络状态，我使用了 thunk，这不是由`useReducer`钩子提供的。那我是怎么处理的呢？

首先，像以前一样，我需要创建上下文:

```
// contexts/AppStatusContext.ts

export const AppStatusContext = createContext({ isOnline: false })
export const AppStatusProvider = AppStatusContext.Provider 
```

然后像 auth 一样，我开始编写一个容器来处理逻辑。这时候我才意识到，我甚至不需要减速器了:

```
// components/AppStatusContainer.tsx

export const AppStatusContainer: FC = ({ children }) => {
  const [isOnline, setIsOnline] = useState(true)

  const setOffline = useCallback(() => {
    setIsOnline(false)
  }, [])
  const setOnline = useCallback(() => {
    setIsOnline(true)
  }, [])

  useEffect(() => {
    window.addEventListener('offline', setOffline)
    window.addEventListener('online', setOnline)

    return () => {
      window.removeEventListener('offline', setOffline)
      window.removeEventListener('online', setOnline)
    }
  })

  return <AppStatusProvider value={{ isOnline }}>{children}</AppStatusProvider>
} 
```

因此，我不仅摆脱了额外的依赖，还降低了复杂性！这个特定的 thunk 可以简单地用一个`useEffect`钩子替换。

* * *

这就是我如何在短短的几个步骤中(大约一个小时)设法减少了我的应用程序包的大小，并去掉了一些不必要的复杂逻辑。这里的教训是，无论一个库有多有用，它都可能会发生，你不需要它。你只需要保持开放的心态，注意它何时发生:)

我希望我的经历能给你们一些人勇气去尝试新事物，发现新的开发经验！

PS:钩子超赞！如果你还没有，你完全应该开始使用它们。