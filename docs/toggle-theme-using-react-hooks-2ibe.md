# 使用 React 挂钩切换主题

> 原文：<https://dev.to/email2vimalraj/toggle-theme-using-react-hooks-2ibe>

最初发表于我的博客:[http://www.vimalselvam.com](http://www.vimalselvam.com)

我试图在我工作的一个应用程序中实现黑暗模式。互联网上大多数可用的例子要么使用`styled-components`要么使用任何其他的`css-in-js`概念。我正在开发的应用程序还没有`css-in-js`。所以我想保持简单。因此，首先想到的是使用 React 的[上下文](https://reactjs.org/docs/context.html) API。为什么是语境？根据 react 文档:

> 上下文提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性。

是的，这个定义是不言自明的。我们不必将道具传递给每个组件，并沿着组件树向下传递。想到这就维持了一个全局状态。

要创建一个上下文对象，我们应该使用 React 的`createContext`方法，并将默认值传递给它(即初始状态)。

```
const ThemeContext = React.createContext(initialState) 
```

`ThemeContext`对象包含一个`ThemeContext.Provider`组件，因此子组件可以使用变更/状态。

我们已经基本了解了我们需要进一步做的事情。让我们构建一个可以在亮暗模式之间切换的应用程序。请注意，一旦我切换到一个特定的模式，下次我访问应用程序，它应该保持相同的模式。也就是说，如果我切换到轻模式，下次我访问时，它应该只在轻模式下显示应用程序。所以我们将使用`localStorage`来保存选中的主题。

创建 react 应用程序:

```
create-react-app my-app
cd my-app
npm start 
```

在您最喜欢的编辑器中打开它。

在`src`目录下创建一个名为`theme-context.js`的文件。

```
const themes = {
  dark: {
    backgroundColor: 'black',
    color: 'white'
  },
  light: {
    backgroundColor: 'white',
    color: 'black'
  }
} 
```

我想保持简单。我维护了两种主题类型`dark`和`light`，分别带有一些简单的背景和前景色。因此，如果我切换到`dark`模式，那么我应该我的页面的背景色改为`black`，前景色改为`white`，如果是浅色，反之亦然。

接下来，让我把我的初始状态放到`createContext`里。

```
const initialState = {
  dark: false,
  theme: themes.light,
  toggle: () => {}
}
const ThemeContext = React.createContext(initialState) 
```

然后，让我们创建一个用`ThemeContext.Provider`组件包装所有子组件的方法，并导出这个方法和我们之前创建的实际的`ThemeContext`对象。

```
function ThemeProvider({ children }) {
  const [dark, setDark] = React.useState(false) // Default theme is light

  // On mount, read the preferred theme from the persistence
  React.useEffect(() => {
    const isDark = localStorage.getItem('dark') === 'true'
    setDark(isDark)
  }, [dark])

  // To toggle between dark and light modes
  const toggle = () => {
    const isDark = !dark
    localStorage.setItem('dark', JSON.stringify(isDark))
    setDark(isDark)
  }

  // Filter the styles based on the theme selected
  const theme = dark ? themes.dark : themes.light

  return (
    <ThemeContext.Provider value={{theme, dark, toggle}}>
      {children}
    </ThemeContext.Provider>
  )
}

export { ThemeProvider } 
```

所以最后的`theme-context.js`看起来是这样的:

```
import React from 'react'

const themes = {
  dark: {
    backgroundColor: 'black',
    color: 'white'
  },
  light: {
    backgroundColor: 'white',
    color: 'black'
  }
}

const initialState = {
  dark: false,
  theme: themes.light,
  toggle: () => {}
}
const ThemeContext = React.createContext(initialState)

function ThemeProvider({ children }) {
  const [dark, setDark] = React.useState(false) // Default theme is light

  // On mount, read the preferred theme from the persistence
  React.useEffect(() => {
    const isDark = localStorage.getItem('dark') === 'true'
    setDark(isDark)
  }, [dark])

  // To toggle between dark and light modes
  const toggle = () => {
    const isDark = !dark
    localStorage.setItem('dark', JSON.stringify(isDark))
    setDark(isDark)
  }

  const theme = dark ? themes.dark : themes.light

  return (
    <ThemeContext.Provider value={{ theme, dark, toggle }}>
      {children}
    </ThemeContext.Provider>
  )
}

export { ThemeProvider, ThemeContext } 
```

打开`index.js`，用我们的`ThemeProvider`包裹`App`组件。以便主题状态可以与`App`组件中所有可用的子组件共享。

修改后的`index.js`看起来像:

```
import React from 'react'
import ReactDOM from 'react-dom'
import './index.css'
import App from './App'
import * as serviceWorker from './serviceWorker'
import { ThemeProvider } from './theme-context'

ReactDOM.render(
  <ThemeProvider>
    <App />
  </ThemeProvider>,
  document.getElementById('root')
)

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister() 
```

让我们转到`App.js`，在`return`语句前添加以下内容:

```
const { theme, toggle, dark } = React.useContext(ThemeContext) 
```

`useContext`是 React 的钩子 api，相当于`ThemeContext.Consumer`组件。点击了解更多[。](https://reactjs.org/docs/hooks-reference.html#usecontext)

然后在`<img>`标签前添加一个按钮来切换主题:

```
<button
  type="button"
  onClick={toggle}
  style={{
    backgroundColor: theme.backgroundColor,
    color: theme.color,
    outline: 'none'
  }}
>
  Toggle to {!dark ? 'Dark' : 'Light'} theme
</button> 
```

现在在`header`标签中，添加以下属性:

```
style={{ backgroundColor: theme.backgroundColor, color: theme.color }} 
```

看看您的应用程序(通常它应该运行在 [http://localhost:3000](http://localhost:3000) )。你可以看到背景色变成了`white`，前景色变成了`black`。

点击按钮在`Dark`和`Light`模式之间切换。您可以关闭并重新打开该选项卡，或者打开同一应用程序的新选项卡，主题模式将保持不变。

`App.js`的全部代码:

```
import React from 'react'
import logo from './logo.svg'
import './App.css'
import { ThemeContext } from './theme-context'

function App() {
  const { theme, toggle, dark } = React.useContext(ThemeContext)

  return (
    <div className="App">
      <header
        className="App-header"
        style={{ backgroundColor: theme.backgroundColor, color: theme.color }}
      >
        <button
          type="button"
          onClick={toggle}
          style={{
            backgroundColor: theme.backgroundColor,
            color: theme.color,
            outline: 'none'
          }}
        >
          Toggle to {!dark ? 'Dark' : 'Light'} theme
        </button>
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  )
}

export default App 
```

演示:

[https://codesandbox.io/embed/themecontexthookapi-uumsb](https://codesandbox.io/embed/themecontexthookapi-uumsb)