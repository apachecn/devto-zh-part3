# 如何使用自定义的 React 挂钩轻松添加黑暗模式

> 原文：<https://dev.to/smakosh/how-to-add-dark-mode-easily-with-a-custom-react-hook-10mj>

> 这篇文章最初发表在我的博客上

在这篇简短的文章中，我将用一个定制的 React 钩子在一个小的 React 应用程序中实现黑暗模式，

在我们开始之前，让我们看看这是如何通过三个简单的步骤实现的:

1.  我们将首先检查所选择的主题是否没有存储在 localStorage 上
2.  如果它存在，我们将简单地将其设置为默认主题
3.  否则，我们将不得不使用灯光主题作为默认主题

所以让我们开始吧！

让我们首先设置自定义钩子，我们将使用从 React 导入的`useEffect` & `useState`，我们将在组件第一次挂载时检查 localStorage，然后我们将执行第二步！

```
import { useEffect, useState } from 'react'

export default () => {
  const [theme, setTheme] = useState('light')

  useEffect(() => {
    const localTheme = window.localStorage.getItem('theme')
    if (localTheme) {
      setTheme(localTheme)
    }
  }, [])
} 
```

现在让我们设置一个函数，在主题之间切换，并将选择的主题存储在 localStorage

```
import { useEffect, useState } from 'react'

export default () => {
  const [theme, setTheme] = useState('light')

  const toggleTheme = () => {
    if (theme === 'light') {
      window.localStorage.setItem('theme', 'dark')
      setTheme('dark')
    } else {
      window.localStorage.setItem('theme', 'light')
      setTheme('light')
    }
  }

  useEffect(() => {
    const localTheme = window.localStorage.getItem('theme')
    if (localTheme) {
      setTheme(localTheme)
    }
  }, [])
} 
```

现在一切都好了，我们只需要返回主题值和函数，这样我们就可以在导入自定义钩子时访问它了！

```
import { useEffect, useState } from 'react'

export default () => {
  const [theme, setTheme] = useState('light')

  const toggleTheme = () => {
    if (theme === 'light') {
      window.localStorage.setItem('theme', 'dark')
      setTheme('dark')
    } else {
      window.localStorage.setItem('theme', 'light')
      setTheme('light')
    }
  }

  useEffect(() => {
    const localTheme = window.localStorage.getItem('theme')
    if (localTheme) {
      setTheme(localTheme)
    }
  }, [])

  return [
    theme,
    toggleTheme,
  ]
} 
```

最后，让我们看看在导入它时如何使用它

```
import React from 'react'
import useDarkMode from './useDarkMode'

export default () => {
  const [ theme, toggleTheme ] = useDarkMode()
  return (
    <div
      style={{
        background: theme === 'dark' ? '#000' : '#fff',
        color: theme === 'dark' ? '#fff' : '#000',
      }}
    >
      <button type="button" onClick={toggleTheme}>
        Switch theme
      </button>
    </div>
  )
} 
```

[https://codesandbox.io/embed/mjm4zy7pm8](https://codesandbox.io/embed/mjm4zy7pm8)

如果你想像我在我的网站上做的那样在一个大的应用程序中实现它，你可能必须使用 React Context API 来访问你的应用程序中任何地方的 theme & toggleTheme 值。