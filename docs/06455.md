# 将状态传递给 React 路由器呈现的组件(以及其他有趣的事情)

> 原文：<https://dev.to/halented/passing-state-to-components-rendered-by-react-router-and-other-fun-things-3pjf>

点击博客帖子最令人沮丧的事情之一是，当你可以把答案放在顶部时，却不得不滚动浏览人们冗长的解释。下面是你如何做题目中的事情:

```
<Router>
  <NavLink to="/homepage">Homepage</NavLink>
  <Route path="/homepage" render={props => 
  (<Homepage {...props} pieceOfState={this.state.pieceOfState}/>)
}/>
</Router> 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道更多细节，请继续阅读:)

路由器本身你可以放在任何你想放的地方——但是最有意义的是在你的应用程序中选择一个相当顶层的部分，所以通常在你的 App.js 文件的 render 方法中。正如你在上面看到的，我们使用的导航链接指向这个特定网站或应用程序的主页，而路径是驱动力，它实际上完成了渲染组件的工作。如果您不需要向组件传递任何状态，您通常会看到路由器是这样的:

```
 <Route path='/homepage' component={Homepage} /> 
```

Enter fullscreen mode Exit fullscreen mode

但是在 React 中，传递状态(或者辅助方法)是所有能力的来源——这是 React 如此具有反应性的原因。因此，您将希望使用第一个代码片段来获得您想要的功能。代码中路由路径是使用 render 方法传递一个将呈现主页的内联函数——您可能想知道，为什么我们不能使用代码片段#2 中的常规组件方法传递一个内联函数并获得相同的结果呢？答案是，如果对组件方法使用内联函数，那么每次组件状态改变时，组件方法实际上会卸载并重新装载整个组件。当你可以使用友好的 React 开发人员希望你使用的简洁的渲染方法时，这会创建一个不必要的高能耗程序。

既然这部分已经解决了，下面是前面提到的其他有趣的事情:

**1。通过全 dang 状态**

有时候，在 React 中编写时，很难让代码[保持干燥](https://dev.to/juuh42dias/drydont-repeat-yourself-43g3)。在将特定的状态片段传递给所需的组件时，您可能会发现自己编写了无数次 this.state。有一个有趣的小技巧可以帮助避免这个问题:您可以忽略整个 dang 状态而不指定片段。看起来是这样的:

```
<Homepage state={this.state}/> 
```

Enter fullscreen mode Exit fullscreen mode

那是非常直接的。这是相当的状态前进？无论如何，您可以使用 this.props.state.pieceOfState 来访问该组件内部的状态片段。

**2。活动链接**

对链接进行样式化，使其在用户位于相关页面时做出响应从未如此简单。你可以简单地给 NavLink 一个 activestyle 类(以及任何你想出现的 CSS)，如下:

```
<NavLink to='/homepage' activeStyle={{fontWeight: "bold", color: 'blue'}}>Homepage</NavLink> 
```

Enter fullscreen mode Exit fullscreen mode

React 将处理对用户所在页面的监听。

**3。渲染一个 404**

有时你的站点的用户会变得任性，认为他们大概能猜出可用的路径，所以他们只是输入路径，期望看到它出现。React 很好，不会破坏你的站点，但是不会告诉用户页面不存在。要渲染 404，使用开关标签对路线进行分组是很有用的。

```
<Switch>
    <Route path='/homepage' component={Homepage}/>
    <Route path='/profile' component={Profile}/>
    <Route path='/seaturtles' component={Seaturtles}/>
    <Route component={NoMatch}/>
</Switch> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，组件“NoMatch”没有给定路由，所以所有未定义的路由都将呈现组件，您可以构建组件来呈现您想要的 404 页面外观。你可以把任何东西放在那里。强尼·布拉沃的照片。链接到 404 的维基百科页面。一个永无止境的宪法滚动循环。世界是你的。

**4。重定向**

直观地说，如果您的用户已经登录，您不会希望他们能够导航到“/登录”页面。但是，你也不想让他们看到 404 页。是时候实现重定向了。这是通过指定另一个到“/signin”的路由并给它提供呈现重定向的指令来实现的。观察:

```
<Route path="/signin" render={()=> (<Redirect to='/search'/>)}/> 
```

Enter fullscreen mode Exit fullscreen mode

此代码使用与传递道具相同的渲染方法显示路线，但没有道具本身。匿名函数指向我们的重定向，我们可以指定我们希望用户被发送到的 URL。

**重要提示**

[![note](img/5042a97d475907b6a25a502229d71733.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0RWoT-e5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://pa1.narvii.com/6433/803f6c4eb1ea8986bd705a326609df6ed5c6af93_hq.gif)

您*将*必须将任何和所有路由器元素导入到您打算使用它们的任何文件中。例如，要做这篇文章中列出的所有事情，你需要在你的文件顶部导入适当的项目:

```
import {BrowserRouter as Router, Route, NavLink, Switch, Redirect} from 'react-router-dom'; 
```

Enter fullscreen mode Exit fullscreen mode

感谢您的来访，祝您旅途愉快！