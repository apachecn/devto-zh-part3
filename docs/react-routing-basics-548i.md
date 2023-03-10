# 反应路由基础

> 原文：<https://dev.to/tttaaannnggg/react-routing-basics-548i>

我刚刚学会了制作一个多页面应用程序所需的 React Router 的 veeeeery 最低要求，我非常兴奋能够使用它！我假设您已经对 HTML 和 JSX 有了基本的了解，能够编写一个简单的 React 组件，并且知道如何将属性传递给组件。

我找到的大多数教程会告诉你如何做事情，但没有真正解释各种 JSX 元素是什么或他们做什么，所以我要在这里试一试。

因此，在基本层面上，React Router 所做的是让您基于页面的 URL 呈现不同的组件。我还不确定它在幕后是如何工作的，但是有一点可能很有用，那就是你可以让一些元素(比如一个标题)在所有页面上保持静态，只需重新呈现需要在页面之间改变的内容(内容或 w/e)。

首先我导入了 React 和 React router，然后我使用了 BrowserRouter 元素和几个 Route 元素来设置我的页面。BrowserRouter 是一个内部包含一个元素的元素。嵌套在该元素中的是 Route 元素，它决定当用户的 URL 具有某个值时显示什么。

所以我把它设置成这样:

```
import React from "react";
import { BrowserRouter as Router, Route, Link } from "react-router-dom";

<BrowserRouter>
  <div>
    <Route>
    <Route>
  <div>
</BrowserRouter> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们做得正确，当我们在一个页面上时，一个 Route 元素将被呈现，而当我们在另一个页面上时，另一个 Route 元素将被呈现。相当简单！

但是我们如何指定哪些 URL 切换每个 Route 元素，以及在其中呈现什么呢？我们将属性传递给他们。

比方说，当我们在[www.ourwebsite.com/info](http://www.ourwebsite.com/info)时，我们希望我们的第一个 Route 元素被渲染。我们将通过在 Route 元素中添加一个“path”属性来实现这一点。看起来是这样的:

```
<Route path='/info'> 
```

Enter fullscreen mode Exit fullscreen mode

(当然，假设我们在 ourwebsite.com 托管我们的页面)

为了确保我们得到我们正在寻找的路径，我们应该指定它应该是一个精确的匹配。我们可以这样做:

```
<Route exact={true} path='/info'> 
```

Enter fullscreen mode Exit fullscreen mode

好吧！现在，当我们导航到 ourwebsite.com/info 时，它会知道选择这条路线，并呈现它指定的任何内容。只有一个问题:我们渲染什么？

我们可以通过向它传递另一个属性“render”来告诉它要呈现什么。很简单，是吧？“render”属性只接受一个返回 JSX 元素的回调函数，类似于 React 组件中的“render”函数返回单个 JSX 元素的方式。所以，让它返回一个包含“hello world”的 h1。

```
<Route exact={true} path='/info' render={()=>(<h1>hello world</h1>)} /> 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们导航到/info 时，我们刚刚定义的 Route 元素应该解析为:

```
<h1>hello world</h1> 
```

Enter fullscreen mode Exit fullscreen mode

还可以为主页指定一个 Route 元素！你所要做的就是将“路径”属性设置为“/”，如下:

```
<Route exact={true} path='/' render={()=><h1>this is ourwebsite.com</h1>} /> 
```

Enter fullscreen mode Exit fullscreen mode

这两条路线组织如下:

```
<BrowserRouter>
  <div>
    <Route exact={true} path='/' render={()=>(<h1>this is ourwebsite.com</h1>)} />
    <Route exact={true} path='/info' render={()=><h1>hello world</h1>} />
  </div>
</BrowserRouter> 
```

Enter fullscreen mode Exit fullscreen mode

当你导航到 ourwebsite.com 时，你会得到这个元素:

```
<h1>this is ourwebsite.com</h1> 
```

Enter fullscreen mode Exit fullscreen mode

当你导航到 ourwebsite.com/info,，你会得到这个代替！

```
<h1>hello world</h1> 
```

Enter fullscreen mode Exit fullscreen mode

另一件事:

好的，我还不确定什么是最佳实践，但是我已经有了一个在每个页面上呈现相同标题的策略。我的策略基本上是在 BrowserRouter 上方呈现我们的 header 组件，并将整个组件包装在一个 div 中。大概是这样的:

```
<div>
  <Header />
  <BrowserRouter>
    <div>
      <Route />
      <Route />
    </div>
  </BrowserRouter>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

显然，根据您需要的任何规范填写路由组件。下面的语法也可以，但是我不确定这是不是最佳实践！

```
<BrowserRouter>
  <div>
    <Header />
    <Route />
    <Route />
  </div>
</BrowserRouter> 
```

Enter fullscreen mode Exit fullscreen mode

如果你们中的任何人有任何纠正或建议，我很乐意听到！