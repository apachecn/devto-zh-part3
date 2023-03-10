# 反模式反应:渲染

> 原文：<https://dev.to/jcutrell/react-anti-pattern-renderthing-50nd>

如果您使用 React 做了很多工作，您可能会遇到这种情况:

```
class Tabs extends React.Component {

  constructor(props){
    super(props)
    this.state = {}
  }

  setActiveTab(activeTab){
    this.setState({ activeTab });
  }

  renderTabs(){
    return (
      this.props.tabs.map(tab =>(
        <a onClick={e => this.setActiveTab(tab.id)}
           key={tab.id}
           className={this.state.activeTab == tab.id ? "active" : ""}
        >
          {tab.title}
        </a>
      ))
    )
  }

  render(){
    return (
      <div>
        <p>Choose an item</p>
        <p>Current id: {this.state.activeTab}</p>
        <nav>
          {this.renderTabs()}
        </nav>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

它的用法是这样的:

```
<Tabs tabs={[{title: "Tab One", id: "tab-one"}, {title: "Tab Two", id: "tab-two"}]} /> 
```

Enter fullscreen mode Exit fullscreen mode

而且这个管用！如果这就是你永远需要这个组件的全部，无论如何，到此为止！

但是如果这些代码将来会改变，你很可能会得到一个混乱而冗长的组件。

这里第一个也是最明显的重构味道是`renderTabs`方法。这有几个问题。

首先，`Tabs`组件已经有了一个`render`方法。那么`Tabs` `render`和`renderTabs`的方法有什么区别呢？在一个例子中，我们呈现了标签列表。另一方面，我们正在添加一些上下文。我们经常在过滤列表中看到这种情况。

将这种呈现功能包装在组件中可能很诱人，因为选项卡需要以某种方式与包含的上下文共享状态。

让我们想想如何重构它，使之更容易理解。

另外，让我们假设你已经有了某种测试策略。在这种情况下，我们不打算写测试，但是如果你写了，你可能想要断言你的列表正在呈现，并且点击你的标签呈现出你想要它呈现的。

让我们从移除 renderTabs 方法开始。一开始看起来会很难看。

```
class Tabs extends React.Component {

  constructor(props){
    super(props)
    this.state = {}
  }

  setActiveTab(activeTab){
    this.setState({ activeTab });
  }

  render(){
    return (
      <div>
        <p>Choose an item</p>
        <p>Current id: {this.state.activeTab}</p>
        <nav>
          {this.props.tabs.map(tab =>(
            <a onClick={e => this.setActiveTab(tab.id)}
               key={tab.id}
               className={this.state.activeTab == tab.id ? "active" : ""}
            >
              {tab.title}
            </a>
          ))}
        </nav>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是一个非常好的组件。但是在将来，你可能会在其他地方使用同样的标签式按钮，所以让我们看看我们是否能让这个按钮共享。

让我们来看一个单独的选项卡。

```
<a onClick={e => this.setActiveTab(tab.id)}
   key={tab.id}
   className={this.state.activeTab == tab.id ? "active" : ""}
>
  {tab.title}
</a> 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们把这个组件做成一个独立的功能组件。(换句话说，我们希望组件带道具，但不需要它有自己的状态。)

```
const TabButton = ({ onClick, active, title, tabId, ...props}) => (
  <a onClick={e => {e.preventDefault(); props.onClick(tabId)}}
    {...props}
    className={active ? "active" : ""}
  >
    {title}
  </a> ) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经有了一个功能组件，我们可以将它集成到我们最初的选项卡组件中。

```
const TabButton = ({ onClick, active, title, tabId, ...props}) => (
  <a onClick={e => {e.preventDefault(); props.onClick(tabId)}}
    {...props}
    className={active ? "active" : ""}
  >
    {title}
  </a> )

class Tabs extends React.Component {
  constructor(props){
    super(props)
    this.state = {}
  }

  setActiveTab(activeTab){
    this.setState({ activeTab });
  }

  render(){
    const { tabs } = this.props;
    const { activeTab } = this.state;
    return (
      <div>
        <p>Choose an item</p>
        <p>Current id: {this.state.activeTab}</p>
        <nav>
          {this.props.tabs.map(tab =>(
            <TabButton onClick={this.setActiveTab}
               active={activeTab == tab.id}
               tabId={tab.id}
               key={tab.id}
               title={tab.title}
            />
          ))}
        </nav>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

那么我们在这里真正得到了什么？

*   移除不必要的/令人困惑的 renderTabs 按钮
*   创建了一个不依赖于任何外部状态的可重用的 TabButton 组件
*   `Tabs`接口的 API 没有变化
*   更容易推理和分离关注点——两个较小的组件在一个较大的组件之上。

这个例子是人为的，很小，但是你几乎肯定会找到怪物出现的地方。

重构模式如下所示:

1.  通过将代码移回原始渲染中来移除 monster `renderThing`方法。如果代码合理，就此打住。
2.  隔离渲染输出的子集以从中创建新组件。(请注意，您可能可以直接转到这一步并跳过第 1 步，但我喜欢先将它移回 render 方法中，看看让它留在那里是否有意义。)
3.  努力分离那些可以消失的状态。理想情况下，瞄准一个功能组件；然而，要小心一个*虚荣心功能组件*，在这里，您保存了应该在父组件的子组件中的状态，这样您就可以使它“功能化”这比拥有两个设计良好的有状态组件要糟糕得多。
4.  将新组件合并到以前的组件中，替换标记。如果您发现自己将太多的东西直接传递到子组件中，那么您可能应该在第一步就停止，而根本不将组件抽象出来。

知道何时将一个组件或例程抽象成它自己的专用组件可能很难。有时候，纯粹是偏爱；没有一个正确的方法。当有疑问时，更小的组件更容易推理，但是抽象应该有目的。

你有兴趣看到关于哪些重构模式的文章？评论一下，让我知道！