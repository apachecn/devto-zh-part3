# 反应路由器 V4

> 原文：<https://dev.to/vish448/react-router-v4-4hki>

React Router 是导航组件的集合，这些组件以声明方式与您的应用程序组合在一起。

### 先决条件

React 路由器需要首先安装 react 路由器包才能使用它。

> npm 安装-保存 react-router-dom

### 插入到组件中

```
import {
    BrowserRouter,
    Route,
  } from 'react-router-dom' 
```

**BrowserRouter** :将整个 nav 打包到这个包装器中
**Route** :创建指向专用组件的单独路由。它需要两个参数**路径**和**组件**

## 在行动

```
 import {
    BrowserRouter,
    Route,
  } from 'react-router-dom'

import Nav from './nav'

class App extends React.Component{
    render(){
        return(
            <BrowserRouter>
                <div className ="container">
                    <Nav />
                    <Route exact path="/" component = {home} />
                    <Route path="/about" component = {about} />
                    <Route path="/contact" component = {contact} />
                </div>
            </BrowserRouter> 
        )
    }
} 
```

### 创建链接

一旦我们准备好指向单个组件的路由器，我们就可以创建指向该路由的锚链路。

让我们称之为 nav.js。在这个文件中，我们相信添加我们所有的路线链接。

```
 import React from 'react'
import {
    Link,
    NavLink
  } from 'react-router-dom'

function nav(){
    return(
        <ul className='nav'>
        <li><NavLink exact activeClassName ='active' to ='/'>Home</NavLink> </li>
        <li><NavLink activeClassName ='active' to='/about'>About</NavLink></li>
        <li><NavLink activeClassName ='active' to='/contact'>Contact</NavLink></li>
        </ul>
    )

}
module.exports = nav 
```

我们首先从 react-router-dom 导入 Link 和 NavLink 组件。Navlink 有特殊的标签，有助于创建更有意义的链接。

我们可以将**到**属性传递给 **NavLink** ，它主要是到路线的路径。我们还可以传递 **activeClassName** 属性，并传递一些样式给它。

我们还在根路径中添加了**精确的**属性。这是必要的，因为我们在所有其他路线前面都有“/”。所以为了避免混淆，我们告诉 react 只执行那个路径。

即

> 。active { font-weight:bold；}

我们在这里创建了三条路径

*   /
*   /关于
*   /联系人

如果我们创建专用组件，它将被定向到它们的专用路径。在这里，我们必须将**精确的**添加到我们的**根**路由中，否则所有其他链接都将加载该组件。

## 404 路线

我们可以使用“react-route-dom”的**开关**组件添加 404 未找到的路由

```
 import {
    BrowserRouter,
    Route,
    Switch
  } from 'react-router-dom'

import Nav from './nav'

class App extends React.Component{
    render(){
        return(
            <BrowserRouter>
                <div className ="container">
                    <Nav />
                    <Switch>
                        <Route exact path="/" component = {home} />
                        <Route path="/about" component = {about} />
                        <Route path="/contact" component = {contact} />
                        <Route render= {()=> <p>Not found</p> />
                    </Switch>
                </div>
            </BrowserRouter> 
        )
    }
} 
```

因此，每当它找不到路由，它将呈现未找到。我们没有创建全新的类，而是直接从我们的 route 中呈现文本。

## Codesandbox

[https://codesandbox.io/embed/03xyv16jw0](https://codesandbox.io/embed/03xyv16jw0)