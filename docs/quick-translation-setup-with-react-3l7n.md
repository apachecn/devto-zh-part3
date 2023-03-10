# React 快速翻译设置

> 原文：<https://dev.to/damcosset/quick-translation-setup-with-react-3l7n>

# 简介

作为我的 InvoiceMaker 应用程序开发的一部分，我想要一个法语版本的软件。在 React 应用程序中，有很多库/框架可以用来处理翻译。我想我真的不需要添加更多的包来解决这个问题，我可以自己编写一些简单的代码。所以，在这里！

## 我想要什么

我想要非常简单的东西。我会看看用户的浏览器语言。如果语言是法语( *fr-FR* 或 *fr* )，我会将应用程序的语言设置为法语。否则，它会是英文的。

我知道法国有不同的风味，但现在，处理来自法国的法语正是我所需要的。

## 如何？

首先，让我们创建一个 react 应用程序。我们不需要太多，我将简单地展示如何开始。我们将使用*创建-反应-应用*:

运行:`create-react-app translations-setup`

现在让我们进入新创建的目录并运行`npm start`。

### 获取当前语言

首先，我们需要知道用户的浏览器使用的是哪种语言。修改 *App.js* 添加如下代码:

```
import React from 'react'
import logo from './logo.svg'
import './App.css'
let frenchCodes = ['fr-FR', 'fr-fr', 'fr']
let language = navigator.language || navigator.userLanguage
if (!frenchCodes.includes(language)) {
    language = 'en-US'
}
// App component below... 
```

在这里，我们定义我们将要使用的语言。我们在*窗口.导航器*对象上使用*语言*属性。

我已经把法国代码放在一个数组里了。有些导航器可能会以小写形式返回(我相信有些版本的 Safari 会这样做)。

然后，如果语言没有包含在数组中，我知道这不是用户的浏览器没有法语。因此，我将语言设置为美国英语。

### JSON 文件进行翻译

接下来，我们将创建存储翻译的文件。在 *src* 文件夹中创建一个名为 *i18n* 的文件夹。在该文件夹中，创建两个 JSON 文件， *en-US.json* 和 *fr-FR.json* ，以及一个名为 *index.js* 的 js 文件。

JSON 文件将保存各自语言的翻译:

*   fr-FR.json

```
{  "appTitle":  "Modifiez src/App.js et sauvegarder pour recharger.",  "appLink":  "Apprenez React",  "newPageTitle":  "Nouvelle Page",  "english":  "Anglais",  "french":  "Français"  } 
```

*   en-US.json

```
{  "appTitle":  "Edit src/App.js and save to reload.",  "appLink":  "Learn React",  "newPageTitle":  "New Page",  "english":  "English",  "french":  "French"  } 
```

键名完全是任意的。您可以随意称呼它们，只要它们对您有意义，因为这是我们将在应用程序中使用的。

最后，我们将把翻译文件导入一个独特的 *index.js* 文件:

```
import fr_FR from './fr-FR.json'
import en_US from './en-US.json'
const translations = {
    'fr-FR': fr_FR,
    'en-US': en_US
}

export default translations 
```

太好了，我们的翻译现在可以从一个文件中获得。

### 上下文

现在，我们需要把它们放在一起。我们需要在应用程序的所有部分访问当前语言。为此，我们将使用*上下文*。

首先，让我们创建我们的上下文。创建一个新的 *LanguageContext.js* 文件，并将下面的代码复制到里面:

```
import React from 'react'

const LanguageContext = React.createContext()

export default LanguageContext 
```

接下来，我们将把 App 组件包装在上下文提供者中，并设置一个状态来保存我们正在使用的语言。该状态将被传递给上下文提供者，以便在整个应用程序中使用。我还将添加两个按钮来轻松地更改应用程序的当前语言。你的 *App.js* 文件应该是这样的:

```
import React, { useState } from 'react'
import logo from './logo.svg'
import './App.css'
import NewComponent from './NewComponent'
import translations from './i18n/'
import LanguageContext from './LanguageContext'
let frenchCodes = ['fr-FR', 'fr-fr', 'fr']
let language = navigator.language || navigator.userLanguage
if (!frenchCodes.includes(language)) {
    language = 'en-US'
}

function App() {
    const [lng, setLng] = useState(language)
    return (
        <LanguageContext.Provider value={lng}>
            <div className='App'>
                <header className='App-header'>
                    <img src={logo} className='App-logo' alt='logo' />
                    <p>{translations[lng]['appTitle']}</p>
                    <a
                        className='App-link'
                        href='https://reactjs.org'
                        target='_blank'
                        rel='noopener noreferrer'>
                        {translations[lng]['appLink']}
                    </a>
                    <NewComponent />
                    <button onClick={() => setLng('fr-FR')}>
                        {translations[lng]['french']}
                    </button>
                    <button onClick={() => setLng('en-US')}>
                        {translations[lng]['english']}
                    </button>
                </header>
            </div>
        </LanguageContext.Provider>
    )
}

export default App 
```

为了在这里使用我们的翻译，我们从 *i18n/index.js* 导入翻译。然后，为了获得当前语言，我们使用了 *lng* 变量，存储在 state 中，并在我们的上下文中用作值。所以，`translations[lng]`会给我们当前语言的翻译。然后，我们添加我们希望获得适当值的键。

你可能已经注意到在我们的*应用*中有一个新的组件。我已经添加了 *NewComponent* 组件，这个组件使用 useContext 钩子来获得正确的翻译:

```
import React, { useContext } from 'react'
import LanguageContext from './LanguageContext'
import translations from './i18n'

const NewComponent = function() {
    const lng = useContext(LanguageContext)
    return (
        <div>
            <h1>{translations[lng]['newPageTitle']}</h1>
        </div>
    )
}

export default NewComponent 
```

您需要导入 LanguageContext 和翻译文件。通过为 useContext 提供 LanguageContext，您将能够在应用程序中的任何地方使用当前语言。

在那里你有一个简单快捷的方法来处理翻译，而不需要添加任何新的包或者库到你的项目中。😉

你可以在 Github 上找到代码。

找点乐子