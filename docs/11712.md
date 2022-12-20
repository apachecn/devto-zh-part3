# JS 中的 CSS(第 2 部分)

> 原文：<https://dev.to/thorning_m/css-in-js-part-2-ee5>

在我的上一篇博客文章中，我写了我是如何开始使用 T2 情绪而不是 Sass 的。最初我使用的是 Emotion 的框架无关包，但我现在已经改用他们专门为 React 创建的版本，因为它添加了一些我喜欢的额外功能！这篇文章是关于如何使用情绪和反应，因此，我假设如果你正在阅读这篇文章，那么你已经知道如何使用反应。

让我们开始吧，你可以用 NPM 安装包:

```
npm install @emotion/core --save-dev 
```

然后，您需要对您的 Babel 设置进行一些更改，以使您的项目能够使用这个包。

## 通天塔

有两种方法可以让你的应用程序与 *@emotion/core* 一起工作。最快的方法是在文件的顶部导入 Emotion 的`jsx`函数，并在它上面的一行包含一个 [JSX 杂注](https://emotion.sh/docs/css-prop#jsx-pragma)，如下所示:

```
/** @jsx jsx */
import { jsx } from '@emotion/core' 
```

该 pragma(注释中的部分)告诉 Babel 使用 Emotion 的`jsx`函数来代替 React 的 *createElement* 。有了导入中的这些行，你就可以在文件中任何你以前使用过 *className* 的地方使用 *css* 属性。这是一个很好的解决方案，如果你只是想尝试一下，或者如果你没有权限访问你的巴别塔设置，这是一个有点痛苦的每个文件都这样做，所以如果可能的话，我建议使用第二种方法，即添加巴别塔预置。这很容易做到，只需用 NPM 安装它，然后将它包含在你的*中。babelrc* 或 *babel.config.js* :

```
{
  "presets": ["@emotion/babel-preset-css-prop"]
} 
```

如果你正在使用 Gatsby(就像我在这个网站上一样),你可以使用一个插件。

一旦你设置了巴别塔预设，你就不再需要在你的文件顶部包含编译指示或者导入 *jsx* 函数，你的代码库中的每个组件现在都可以使用 *css* prop。

## CSS 道具

css prop 以几种方式接受你的风格，这给了你在编写组件时更多的灵活性。

它将接受对象样式:

```
const styles = {
  color: 'red',
  marginTop: '10px'
}

export default function HelloCode(props) {
  return <h1 css={styles}>Hello Code</h1> } 
```

它也将接受用 *@emotion/core* 的`css`函数:
创建的字符串样式

```
import { css } from '@emotion/core'

const styles = css`
  color: red;
  margin-top: 10px;
`
export default function HelloCode(props) {
  return <h1 css={styles}>Hello Code</h1> } 
```

与框架不可知版本中的 *css* 函数不同，*这个* `css`函数不返回计算出的类名字符串；它返回一个“低级”的情感理解的对象，因此可以与其他基于情感的风格组合。因此，重要的是不要试图使用由 Emotion 的框架无关版本导出的 *css* 函数。

您还可以将一组样式传递给`css`属性。样式从左到右应用，任何重复的规则都被覆盖:

```
import { css } from '@emotion/core'

const blue = css`
  color: blue;
  margin-top: 10px;
`
const red = css`
  color: red;
`
export default function HelloCode(props) {
  return <h1 css={[blue, red]}>Hello Code</h1> }
//Text will be red 
```

这对于使用组件的状态或属性通过组件的逻辑来控制样式非常有用。在下面的例子中，我使用了一个带有 React 的`useReducer`和`useEffect`钩子的 reducer，在组件挂载五秒钟后给组件添加了一个类:

```
import React, { useEffect, useReducer } from 'react'
import { baseStyle, animationStyle } from './style'

export default function HelloCode() {

  const reducer = (state, action) => {
    switch (action.type) {
      case 'addClass':
        return {
          style: [...state.style, action.payload],
        }
      default:
        return state
    }
  }

  const initialState = {
    style: [baseStyle]
  }

  const [state, dispatch] = useReducer(reducer, initialState)

  function animate() {
    setTimeout(() => {
      dispatch({
        type: 'addClass',
        payload: animationStyle,
      })
    }, 5000)
  }

  useEffect(() => {
    animate()
  }, [])

  return <h1 css={state.style}>Hello Code</h1> } 
```

所以你可以看到`useEffect`在组件挂载时调用了`animate`函数。这个函数使用`setTimeout`在`5000`毫秒的延迟后分派`addClass`动作。动作的有效负载包含对导入到文件顶部的`animationStyle`的引用。reducer 用`addClass`匹配动作类型，并用一个包含来自`action.payload`的`state.style`和`animationStyle`的新数组更新组件的状态。

## 全局分量

我通常认为将 CSS 样式限制在组件范围内是个好主意。当编写 React 应用程序时，您从最小的粒度级别开始构建；创建一个控制整个应用程序样式的大型样式表违背了这种方法论(在我看来)。然而，保持应用程序中 UI 外观的一致性仍然很重要。有两个选项可以帮助解决这个问题，这里是第一个。

*@emotion/core* 提供了一个`Global`组件，可以用来在你的站点上应用 CSS 规则。是这样用的:

```
import { Global, css } from '@emotion/core'

export default function HelloCode() {
  return (
    <div>
      <Global
        styles={css`
          h1 {
            color: red;
          }
        `}
      />
      <h1>Hello Code</h1>
    </div>
  )
} 
```

这很有趣，因为你可以通过渲染一个`Global`组件来完全改变你的站点的样式。React 的优势之一是它使得有条件地渲染子组件变得毫不费力，使用`Global`组件作为子组件，你可以很容易地改变你的站点的整个外观，只需按一个按钮:

```
import React, { useState } from 'react'
import { Global, css } from '@emotion/core'

const nightModeStyle = css`
  body {
    background: black;
  }
  h1 {
    color: white;
  }
`
export default function HelloCode() {
  const [nightMode, setNightMode] = useState(false)

  return (
    <div>
      {nightMode && <Global styles={nightModeStyle} />}
      <button onClick={() => setNightMode(!nightMode)}>Night Mode</button>
      <h1>Hello Code</h1>
    </div>
  )
} 
```

轻微免责声明用这个，我没试过(还没)；如果你碰巧试了一下，但没有用，请告诉我！

## 主题化

通过主题化，你可以创建一个对象，其中包含你想在任何组件的 *css* prop 中访问的设置。这有助于保持一致的外观，并使品牌的颜色变化更加容易，因为你只需要在一个地方而不是在每个组件中改变值。

如果你想带着感情使用主题化，那么你首先需要用 NPM 安装它

```
npm install emotion-theming --save-dev 
```

*情感主题*使用 React 的上下文 API，所以首先你需要包装所有你想在`ThemeProvider`中访问主题的组件。这是标准的上下文内容，只是它使用了`theme` :
而不是*值*

```
import { render } from 'react-dom'
import App from './app'
import { ThemeProvider } from 'emotion-theming'

const theme = {
  primaryTextColor: 'red'
  h1TopMargin: '10px'
}

render(
  <ThemeProvider theme={theme}>
    <App />
  </ThemeProvider>,
  document.getElementById('root')
) 
```

现在，让我们以之前的例子为例，但这次我们将从主题:
中获取值

```
import { css } from '@emotion/core'

const styles = theme => css`
  color: ${theme.primaryTextColor};
  margin-top: ${theme.h1TopMargin};
`
export default function HelloCode(props) {
  return <h1 css={styles}>Hello Code</h1> } 
```

`css` prop 也接受一个函数，这个函数是用`theme`作为参数调用的。当 React 看到这个时，它会沿着组件树往回走，直到找到一个`ThemeProvider`，并从它的道具中获取主题对象。因为这是一个 JavaScript 对象，所以可以像传递任何其他对象一样传递它。这里有另一个来自早期的例子:

```
import { css } from '@emotion/core'

const blue = theme => css`
  color: blue;
  margin-top: ${theme.h1TopMargin};
`
const red = theme => css`
  color: ${theme.primaryTextColor};
`
export default function HelloCode(props) {
  return (
    <h1 css={theme => (
      [blue, red].map(style => style(theme))
    )}>
      Hello Code
    </h1>
  )
}
//Text will be red 
```

这里我们使用 map 将主题对象传递给数组中的每个函数。我相信你可以看到这有多强大；任何`css`道具都可以很容易地变成一个函数，只需要在它前面加上`theme =>`就可以访问你的主题对象中的任何东西！

* * *

这涵盖了我最近带着感情做的所有事情。他们的文档中有更多我没有涉及的内容，但我已经谈到了我发现最有用的内容。尽管如此，看一看，如果你看到或知道我没有提到的任何你认为有用的东西，请让我知道。干杯！👍