# 范围样式

> 原文：<https://dev.to/nickytonline/scoped-style--36n3>

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[sadick 254](https://github.com/sadick254)/[scope-style](https://github.com/sadick254/scoped-style)

### js 库中的一个小 css🚀

<article class="markdown-body entry-content container-lg" itemprop="text">

# 范围样式

Scoped style 是下一代微型 css-in-js 库，可以帮助你设计你的组件。使用您已经习惯的 css 的全部功能。

## 与…一起工作

*   [反应](https://reactjs.org/)
*   [预先行动](https://preactjs.com/)
*   [Hyperapp](https://github.com/jorgebucaran/hyperapp)
*   [地狱](https://infernojs.org/)

## 装置

```
npm i scoped-style
// or
yarn add scoped-style
```

Enter fullscreen mode Exit fullscreen mode

## 使用

```
import scoped from 'scoped-style';
// for react
import React from 'react';
const styled = scoped(React.createElement);
//

// for Preact
import { h } from 'preact';
const styled = scoped(h);
//

// for Hyperapp
import { h } from 'hyperapp';
const styled = scoped(h);
//

// for Infernojs
import { createElement } from 'inferno-create-element';
const styled = scoped(createElement);
//

// define global css
styled.global`
 * {
 margin: 0;
 }

 html,
 body {
 width: 100%;
 height: 100%;
 }
`;

// and
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/sadick254/scoped-style)

JS 街区 CSS 上的新小子。

可以很好地玩预动作，反应，超动作和下动作。

React 自述文件中的一个例子:

```
import React from "react"
import scoped from "scoped-style"

const styled = scoped(React.createElement)

// define global css
styled.global`
  * {
    margin: 0;
  }

  html,
  body {
    width: 100%;
    height: 100%;
  }
`;

// and scoped css
const Button = styled("button")`
  background: ${props => props.primary ? "orange": "gray"};
  border: none;
  border-radius: 2px;
  :hover {
    padding: 10px;
  }
`

const App = () => (
  <div>
    <Button primary>Login</Button>
  </div>
)

// Your rendering code 
```

Enter fullscreen mode Exit fullscreen mode