# 用 React 钩子管理浏览器 Cookies

> 原文：<https://dev.to/devhammed/manage-browser-cookies-with-react-hooks-22ba>

[![Browser Cookies](img/c07ed3dcb9061e04de9e03e99a132312.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qbuE2gIG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/f/f1/2ChocolateChipCookies.jpg/1200px-2ChocolateChipCookies.jpg)

所以这个星期，我创造了我的第一个 NPM 包和我的第一个定制反应钩！！！

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[devhammed](https://github.com/devhammed)/[use-cookie](https://github.com/devhammed/use-cookie)

### 使用 React 钩子获取、设置、更新和删除 Cookie。

<article class="markdown-body entry-content container-lg" itemprop="text">

# [@devhammed](https://dev.to/devhammed) /use-cookie

> 使用 React 钩子获取、设置、更新和删除 Cookie。

[![NPM](img/4dfaaa0b8d8cc6060ea7b2d24bdac613.png)](https://www.npmjs.com/package/@devhammed/use-cookie)[![JavaScript Style Guide](img/c079a598eada2d8f8b76fa166c13a74c.png)](https://standardjs.com)[![Made in Nigeria](img/ee39b868df2cd76675f5b0f4bf9a19fd.png)](https://github.com/acekyd/made-in-nigeria)

## 安装

```
npm install --save @devhammed/use-cookie
```

Enter fullscreen mode Exit fullscreen mode

## 使用

```
import React from 'react'
import ReactDOM from 'react-dom'
import useCookie from '[@devhammed](https://dev.to/devhammed)/use-cookie'
const App = () => {
  const [username, setUsername, deleteUsername] = useCookie('username', 'User')
  return (
    <section>
      <h1>Hello {username}!</h1>
      <p>Edit below input, Your name will be stored in a cookie. you can refresh this page to see how it persists.</p>
      <input
        type='text'
        value={username}
        onChange={(e) => setUsername(e.target.value)}
      />
      <button
        onClick={() => deleteUsername()}
      >
        Delete Cookie
      </button>
    </section>
  )
}

ReactDOM.
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/devhammed/use-cookie)

你想用 React 管理 HTTP Cookies 吗？？？
我创建了`useCookie()` React Hook，你可以用它来获取、设置、更新、删除你的功能组件中的 Cookies。

演示:[https://devhammed.github.io/use-cookie](https://devhammed.github.io/use-cookie)T2:[https://www.npmjs.com/package/@devhammed/use-cookie](https://www.npmjs.com/package/@devhammed/use-cookie)