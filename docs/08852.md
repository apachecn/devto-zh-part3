# 使用 AbortController(带有 React 挂钩和 TypeScript)取消 window.fetch 请求

> 原文：<https://dev.to/bil/using-abortcontroller-with-react-hooks-and-typescript-to-cancel-window-fetch-requests-1md4>

*原贴于[bilaw.al/abortcontroller.html](https://bilaw.al/abortcontroller.html)T3】*

我一直渴望能够在 JavaScript 中取消`window.fetch`请求。这是一件特别有用的事情，尤其是在遵守 [React 的生命周期](https://reactjs.org/docs/state-and-lifecycle.html)的时候，尤其是在引入 React 钩子的时候。

谢天谢地，我们有个东西叫`AbortController`！

```
const abortController = new AbortController()

const promise = window
  .fetch('https://api.example.com/v1/me', {
    headers: {Authorization: `Bearer [my access token]`},
    method: 'GET',
    mode: 'cors',
    signal: abortController.signal,
  })
  .then(res => res.json())
  .then(res => {
    console.log(res.me)
  })
  .catch(err => {
    console.error('Request failed', err)
  })

// Cancel the request if it takes more than 5 seconds
setTimeout(() => abortController.abort(), 5000) 
```

Enter fullscreen mode Exit fullscreen mode

正如您所料，**这将在 5 秒钟后取消请求。很酷，是吧？**

在我们的`catch`中，它会给我们一个`AbortError`错误和消息`The user aborted a request.`，所以我们甚至可以重写我们的错误检查来考虑这个:

```
promise.catch(err => {
  if (err.name === 'AbortError') {
    console.error('Request took more than 5 seconds. Automatically cancelled.')
    return
  }

  // It wasn't that the request took longer than 5 seconds.
  console.error(err.message)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 反应钩子？

当然，让我们深入探讨一下。这是你想要的东西:

```
// src/hooks/useProfileInformation.jsx
import {useState, useEffect} from 'react'

export function useProfileInformation({accessToken}) {
  const [profileInfo, setProfileInfo] = useState(null)

  useEffect(() => {
    const abortController = new AbortController()

    window
      .fetch('https://api.example.com/v1/me', {
        headers: {Authorization: `Bearer ${accessToken}`},
        method: 'GET',
        mode: 'cors',
        signal: abortController.signal,
      })
      .then(res => res.json())
      .then(res => setProfileInfo(res.profileInfo))

    return function cancel() {
      abortController.abort()
    }
  }, [accessToken])

  return profileInfo
}

// src/app.jsx
import React from 'react'
import {useProfileInformation} from './hooks/useProfileInformation'

export function App({accessToken}) {
  try {
    const profileInfo = useProfileInformation({accessToken})

    if (profileInfo) {
      return <h1>Hey, ${profileInfo.name}!</h1>
    } else {
      return <h1>Loading Profile Information</h1>
    }
  } catch (err) {
    return <h1>Failed to load profile. Error: {err.message}</h1>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## React 钩子和 TypeScript？

哦，你！已经拿走了。

```
// src/hooks/useProfileInformation.tsx
import {useState, useEffect} from 'react'

export interface ProfileRequestProps {
  accessToken: string
}

export interface ProfileInformation {
  id: number
  firstName: string
  lastName: string
  state: 'free' | 'premium'
  country: {
    locale: string
  }
}

export function useProfileInformation({accessToken}: ProfileRequestProps): ProfileInformation | null {
  const [profileInfo, setProfileInfo] = useState(null)

  useEffect(() => {
    const abortController = new AbortController()

    window
      .fetch('https://api.example.com/v1/me', {
        headers: {Authorization: `Bearer ${accessToken}`},
        method: 'GET',
        mode: 'cors',
        signal: abortController.signal,
      })
      .then((res: Response) => res.json())
      .then((resProfileInfo: ProfileInformation) => setProfileInfo(resProfileInfo))

    return function cancel() {
      abortController.abort()
    }
  }, [accessToken])

  return profileInfo
}

// src/app.tsx
import React, { ReactNode } from 'react'
import {useProfileInformation, ProfileRequestProps, ProfileInformation} from './hooks/useProfileInformation'

export function App({accessToken}: ProfileRequestProps) : ReactNode {
  try {
    const profileInfo: ProfileInformation = useProfileInformation({accessToken})

    if (profileInfo) {
      return <h1>Hey, ${profileInfo.name}!</h1>
    } else {
      return <h1>Loading Profile Information</h1>
    }
  } catch (err) {
    return <h1>Failed to load profile. Error: {err.message}</h1>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 测试

默认情况下在`jest`和`jsdom`中支持，所以一切就绪。类似于。

```
// src/utils.js
export const getProfileInformation = () => {
  const abortController = new AbortController()
  const response = window
    .fetch('https://api.example.com/v1/me', {signal: abortController.signal})
    .then(res => res.json())
  return {response, abortController}
}

// src/__tests__/utils.test.js
import utils from '../utils'

describe('Get Profile Information', () => {
  it('raises an error if we abort our fetch', () => {
    expect(() => {
      const profile = getProfileInformation()
      profile.abortController.abort()
    }).toThrowError()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 承诺

想看看你如何用`AbortController`来表达承诺吗？看看同事 Josef Blake 写的[make-aboble](https://github.com/jomaxx/make-abortable)

## 逮到你了？

### 陷阱#1:不支持析构

遗憾的是，我们不能这样破坏我们的`new AbortController()`:

```
const {signal, abort} = new AbortController()

window
  .fetch('https://api.example.com/v1/me', {signal})
  .then(res => res.json())
  .then(res => console.log(res))

setTimeout(() => abort(), 5000) 
```

Enter fullscreen mode Exit fullscreen mode

当我们调用`abort()`方法时，它会调用一个`Uncaught TypeError: Illegal invocation`错误，因为它是依赖于`this`的原型实现。

## 结论

不久前我已经阅读了关于`AbortController`的文章，但很高兴我终于有机会充分了解它。令人印象深刻的是，所有浏览器都支持它(当然除了 Safari ),所以你应该可以在你的项目中使用它:)