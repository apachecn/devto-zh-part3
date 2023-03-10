# 请看 HTML 状态

> 原文：<https://dev.to/thekashey/behold-the-html-state-5e8b>

让我们挑选一个简单的例子——几个**标签**和一个简单的**复选框**。但是，不管所有的简单，让我们把它变得更复杂一点，然后优化方法，使每一步都变得越来越好。

让我们试试`Redux`、`setState`、`useState`和...结局会是...出乎意料。

> TL；https://codesandbox.io/s/romantic-http-h6cgj 博士

## App

让我们假设我们的`App`类似于

```
function App() {
  return (
    <div className="App">
      <TabHeader>
        <Tab id={1}>One</Tab>
        <Tab id={2}>Two</Tab>
        <Tab id={3}>Three</Tab>
      </TabHeader>
      <TabContext>
        <Tab id={1}>
          Content of tab1 <CheckBox />
        </Tab>
        <Tab id={2}>
          Content of tab2 <CheckBox />
        </Tab>
        <Tab id={3}>
          Content of tab3 <CheckBox />
        </Tab>
      </TabContext>
    </div>
  );
} 
```

我希望第一个例子越糟糕越好，但我认为这已经足够了...

## Redux

啊 Redux，国家的国家！你控制着真理的唯一来源，为我们所有人发送货物。

当然，使用 Redux 来处理 Tab 组件是一个正确的决定，只要 Redux 始终是一个正确的决定。

```
// TabHeader just renders everything inside
const TabHeader = ({ children }) => <section>{children}</section>; 
// "Stateless/Dumb" Tab component
const TabImplementation = ({ children, onSelect }) => <div onClick={onSelect}>{children}</div>; 
// All the logic is in TabContext
const TabContextImplementation = ({ children, selectedTab }) => (
 <section>
 {React.Children.map(children, (child) => (
   // displaying only "selected" child
   selectedTab === child.props.id  
     ? child
     : null
  ))}
  </section> )

// connecting Tab, providing a `onSelect` action
const Tab = connect(null, (dispatch, ownProps) => ({
  onSelect: () => dispatch(selectTab(ownProps.id))
}))(TabImplementation);

// connecting TabContext, reading the `selectedTab` from the state
const TabContext = connect(state => ({
  selectedTab: state.selectedTab
}))(TabContextImplementation); 
```

这实际上是一个非常简单的例子，说明了`Redux`如何连接你的组件，以及它有多酷。

但是，你知道，对于这么简单的例子，我们可能不需要这么复杂的代码。来优化一下吧！

## 组件状态

要使用*自己的组件状态*，我们只需要移除对 redux 的绑定，并重新连接`App`，使其成为有状态的组件。

```
class App extends React.Component {
  state = {
    selectedTab: 1
  };

  onSelect = (selectedTab) => this.setState({selectedTab});

  render () {
  return (
    <div className="App">
      <TabHeader>
        <Tab id={1} onSelect={() => this.onSelect(1)}>One</Tab>
        <Tab id={2} onSelect={() => this.onSelect(1)}>Two</Tab>
        <Tab id={3} onSelect={() => this.onSelect(1)}>Three</Tab>
      </TabHeader>
      <TabContent selectedTab={this.state.selectedTab}>
        <Tab id={1}>
          Content of tab1 <CheckBox />
        </Tab>
        <Tab id={2}>
          Content of tab2 <CheckBox />
        </Tab>
        <Tab id={3}>
          Content of tab3 <CheckBox />
        </Tab>
      </TabContent>
    </div>
  );
 }
} 
```

这就是我们需要的所有状态管理。还能更简单吗？

## 挂钩

会让它变得更好吗？是啊！他们会的！即使差别很小，从可读性的角度来看也是巨大的。

```
const App = () => {
  const [selectedTab, setSelected] = useState(1);
  return (
    <div className="App">
      <TabHeader>
        <Tab id={1} onSelect={useCallback(() => setSelected(1))}>One</Tab>
        <Tab id={2} onSelect={useCallback(() => setSelected(1))}>Two</Tab>
        <Tab id={3} onSelect={useCallback(() => setSelected(1))}>Three</Tab>
      </TabHeader>
      <TabContent selectedTab={selectedTab}>
        <Tab id={1}>
          Content of tab1 <CheckBox />
        </Tab>
        <Tab id={2}>
          Content of tab2 <CheckBox />
        </Tab>
        <Tab id={3}>
          Content of tab3 <CheckBox />
        </Tab>
      </TabContent>
    </div>
  );
} 
```

## 我们做到了？

第一个和最后一个例子的区别是...不一样。所需的代码量几乎是相同的，好处是无与伦比的，可读性在任何情况下都是完美的。

唯一大的区别是“组件化”——本地状态和钩子标签`State`是**本地**，而 Redux 是全局的。它既不坏，也不好，两者都能满足你的需要。

### 我们是不是忘了什么？

我们做了一个伟大的*反应*的工作，但一个可怜的 *html* 的工作。将处理程序附加到 div 上是有史以来最糟糕的想法——这是绝对不可行的。

> 有没有一种方法既能处理“状态”，同时又能让应用程序“可访问”？

## HTML 状态

让我先给你看代码，然后我会解释它是如何工作的

```
const TabHeader = ({ children, group }) => (
  <>
    {React.Children.map(children, (child, index) => (
      // we will store "state" in a radio-button
      <input
        class="hidden-input tab-control-input"
        defaultChecked={index === 0}
        name={group}
        type="radio"
        id={`control-${child.props.controls}`}
      />
    ))}
    <nav>{children}</nav>
  </> );

const Tab = ({ children, controls }) => (
  // Tabs are controlled not via `div`, `button` or `a`
  // Tabs are controlled via `LABEL` attached to input, and to the tab itself
  <label htmlFor={`control-${controls}`} aria-controls={controls}>
    {children}
  </label> );

const TabContent = ({ children, group }) => (
  <section className="tabs">
    {React.Children.map(children, (child, index) => (
      <section class="tab-section" id={child.props.id}>{child.props.children}</section>
    ))}
  </section> );

const CheckBox = () => <input type="checkbox" />;

const App = () => (
  <div className="App">
    <TabHeader group="tabs">
      <Tab controls="tab1">One</Tab>
      <Tab controls="tab2">Two</Tab>
      <Tab controls="tab3">Three</Tab>
    </TabHeader>
    <TabContent>
      <Tab id="tab1">
        Content of tab1 <CheckBox />
      </Tab>
      <Tab id="tab2">
        Content of tab2 <CheckBox />
      </Tab>
      <Tab id="tab3">
        Content of tab3 <CheckBox />
      </Tab>
    </TabContent>
  </div> ); 
```

仅此而已，完全没有状态管理，比以前好用多了。只有`redio-buttons`跟`labels`和`tabs`，跟他们没多大关系...我不知道它是如何工作的——但它确实工作了。

> 这是沙盒的链接-[https://codesandbox.io/s/romantic-http-h6cgj](https://codesandbox.io/s/romantic-http-h6cgj)

更具体地说——它和[的 reach-ui 标签](https://ui.reach.tech/tabs/)一样好用——试试看，是一样的。这与 Reach 热衷的*真实可及的*体验是一样的。
但是`reach-ui`必须**处理键盘**事件和**焦点管理**来使*标签*正常工作——只有`active`标签头是可聚焦的，你可以通过按`Left` / `Right`来改变标签。

你可能不相信，我举了个例子，它会完全一样。

## 秘密

秘密就在 CSS 中——每个`input`都是视觉上隐藏的*。所以它是存在的，它是可聚焦的，但却是不可见的。而其他一切*都采用* `input` **状态**。* 

```
// input 1 is checked? Then tab 1 should be visible.
.tab-control-input:checked:nth-of-type(1) ~ .tabs .tab-section:nth-of-type(1){
  display: block;
}

// and label should be bold if corresponding radio button is active
.tab-control-input:checked:nth-of-type(1) ~ nav label:nth-of-type(1){
  font-weight: 600;
}

// and focus ring should be teleported to a label
.tab-control-input:focus:nth-of-type(1) ~ nav label:nth-of-type(1) {
  outline: thin dotted;
  outline: 5px auto -webkit-focus-ring-color;
} 
```

结构上有一个限制——`~`操作符需要`input`在一边，而`label` + `tab`在另一边。这意味着所有的输入都在左边，所有的标签都在右边。结果是这样的

```
.tab-control-input:checked ~ .tabs .tab-section {
  display: block;
} 
```

将显示所有选中的`input`的所有`.tabs`，只要它们都符合条件。

在这个例子中，我使用了`:nth-of-type(N)`，但是最好是动态生成 CSS(CSS-in-JS)，使用更多的*独特的*选择器，比如`#category-women-clothing:checked ~ div nav label[for="category-women-clothing"]`(来自 [theurge](https://theurge.com) 的代码片段)，或者像 Reach 中的`input-0-0-1`。

## 结论

所以，惊喜吧！HTML 可以是你的状态管理，你将得到更多的语义和更容易获得的结果。[跟它玩一会儿【T3:)](https://codesandbox.io/s/romantic-http-h6cgj)

你知道——利用平台:)你可能会错过一些东西。*