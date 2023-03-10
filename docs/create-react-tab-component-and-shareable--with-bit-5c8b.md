# 创建 React 选项卡组件并可与 Bit 共享

> 原文：<https://dev.to/kris/create-react-tab-component-and-shareable--with-bit-5c8b>

### 用 Bit 构建可复用的 React Tab 组件

#### 创建一个你的团队可以在你的所有应用中使用的标签组件。

<figure>[![](img/ec2611d1c1ea37bff06598e1d5e753cb.png)](https://bitsrc.io/kriss/reactcomponent/components/tabs) 

<figcaption>摆弄组件，用 Bit</figcaption>

</figure>

安装

选项卡允许将复杂的内容分解成多个部分。例如，用户可以一次浏览一个部分。

让我们创建一个可重用的 react 组件来处理您的选项卡。项目结构将非常基本:

*   应用
*   制表符
*   标签

然后，我们将使用[位](https://github.com/teambit/bit)使其可从其他应用程序中重用。让我们开始吧。

### App 组件

App 是所有标签页的容器，**标签页**呈现其中的任何内容。在最终输出中，每个子元素都呈现为一个选项卡。所有直系子女都需要有一个标签。此外，标签在输出中显示为选项卡名称。

```
import React from 'react';
import Tabs from './Tabs';
import './Tab.css'

const App = () => {
  return (
    <div>
      <h1>React Tabs</h1>
      <Tabs>
        <div label="Home">This is the First Tab</div>
        <div label="Another">Yet another Tab!</div>
        <div label="Yet Another">This one's third</div>
      </Tabs>
    </div>
  );
};

export default App; 
```

Tabs 包含带有标签的 div。**首页，另一个**和**还有另一个**将会是点击的标签项。

子组件可以是任何其他具有有效 JSX 的组件，但是现在让我们保持简单。

### 标签页组件

每个选项卡都是选项卡组件的子组件。这是一个必需的属性，将位于一个数组中。

```
static propTypes = {
    children: PropTypes.instanceOf(Array).isRequired
  }; 
```

首先，我们需要一个状态来跟踪当前项目。当每个选项卡被激活时，使用此状态显示每个选项卡的内容，并以不同的方式设置活动项的样式。

```
state = {
    activeTab: this.props.children[0].props.label
  }; 
```

这里，初始状态被设置为第一个孩子的标签。标签可以作为孩子的道具来访问

我们还需要一个改变活动选项卡的方法。

```
onClickTabItem = (tab) => {
    this.setState({ activeTab: tab });
  }; 
```

在选项卡的呈现方法中，析构子级、活动选项卡和 onclick 方法。

```
const {
      onClickTabItem,
      props: { children },
      state: { activeTab }
    } = this; 
```

此外，定义可变内容，这将存储当前活动标签的内容。在单独的 div 中呈现内容。

让内容；

它将返回两个东西:*选项卡列表*和*选项卡内容*。

映射到子组件，将个体传递到选项卡组件，并创建一个列表。

现在，将所需的道具传递给选项卡组件。

```
render() {
    const {
      onClickTabItem,
      props: { children },
      state: { activeTab }
    } = this;
    let content;
    return (
      <div className="tabs">
        <ol className="tab-list">
          {children.map((child) => {
            const { label } = child.props;
            if (label === activeTab) content = child.props.children;
            return (
              <Tab
                activeTab={activeTab}
                key={label}
                label={label}
                onClick={onClickTabItem}
              />
            );
          })}
        </ol>
        <div className="tab-content">{content}</div>
      </div>
    );
  } 
```

并且在同一个循环中，检查所有标签中的 *activeTab* 状态。 *label_same as _activeTab* 为活动标签。选项卡内容区域内容区域将子项显示为内容。

如果你渴望看到输出，用*返回标签*替换*返回*。现在你仍然会得到一些输出。当然，你不能改变标签。

[![](img/bf64faa81336b3710dce14d50337b04f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wEKrRvTR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/618/0%2An8RhUWtWOjEyRE1a.png)

### 标签组件

我们在**标签**中渲染了**标签**组件。但是，还没有选项卡组件。

传递的属性是 key、activeTab、label 和 onClick。

所需道具: **activeTab** 、 **label** 和 **onClick** 。根据需要用正确类型标记它们。

```
static propTypes = {
    activeTab: PropTypes.string.isRequired,
    label: PropTypes.string.isRequired,
    onClick: PropTypes.func.isRequired,
  }; 
```

选项卡呈现传递的标签。

onClick 函数获取一个标签和传递的 onClick 函数。单击选项卡时，它调用一个传入的 onClick 方法，将标签作为参数。

```
onClick = () => {
    const { label, onClick } = this.props;
    onClick(label);
  }; 
```

Tab component 返回带有传递标签的组件。

它从传递的 prop 中获得了高于 *onClick* 方法、*标签*和 *activeTab* 的方法。所有项目都有一个默认类别'*选项卡-列表-项目*'。' *tab-list-active* '是活动项目的类。现在，如果*活动标签*与*标签相同，则将它附加到类列表中。注意类名之间的空格。* 

```
render() {
    const {
      onClick,
      props: { activeTab, label }
    } = this;

    let className = 'tab-list-item';

    if (activeTab === label) {
      className += ' tab-list-active';
    }

    return (
      <li className={className} onClick={onClick}>
        {label}
      </li>
    );
  } 
```

类名的所有 CSS 都在 index.css 中，如果您想让输出看起来像下面这样，请使用相同的类名。

### 用 Bit 使组件可重用

<figure>[![](img/754c2115ffad43ce2fb4fd90eca638b5.png)](https://bitsrc.io/components) 

<figcaption>组件带位:选择、播放、使用</figcaption>

</figure>

[**Bit**](http://bitsrc.io) 是一个基于云的组件平台。

它帮助您的团队组织和共享可重用的组件，这些组件可用于更快地构建新的应用程序。

组件可以与来自不同项目和库的 Bit 共享，并且可以在您的代码中发现、使用和安装。

简单地说，您可以将 Bit 视为您的组件“Lego box ”,您可以在其中共享来自任何项目的组件，然后找到并使用它们来构建新的东西。有用吧？还有很多，但我们现在不会深入讨论。

首先，[登录到 Bit](https://bitsrc.io/signup) 并创建您的组件集合。

现在我们的系列已经准备好了；让我们使用 Bit 从它所在的存储库中快速共享组件(Bit 发挥了“魔力”；它会自动隔离组件，并将其共享到云，无需更改任何代码或文件！).

我们来分享一下 tab 组件。

### 安装钻头

如果您还没有 Bit，使用
进行全局安装

```
npm install -g bit-bin 
```

### 为项目初始化位

用
初始化 Bit 工作空间

```
bit init 
```

这个命令添加 bit.json 和。位图文件，这样 Bit 就可以开始跟踪和隔离存储库中的组件。

在下面的位文档中，我们需要组件的入口点

[![](img/c5e52c58d6846440a54dfc470b715a9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M8jq4gTM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/809/1%2AYCwMXILTjMCHPMcQPl7vyw.png)

创建 **index.js 并从 App.js**
中移走所有代码

```
import React from 'react';
import Tabs from './Tabs';
import './Tab.css'

const Tab = () => {
  return (
    <div>

      <Tabs>
        <div label="Home">This is the First Tab</div>
        <div label="Another">Yet another Tab!</div>
        <div label="Yet">This one's third</div>
      </Tabs>
    </div>
  );
};

export default Tab; 
```

现在，让 Bit 跟踪你放在 src/components 目录中的所有组件。

```
bit add src/components/\* 
```

[为工作区设置一个 Bit 编译器](https://bitsrc.io/bit/envs)(我用 react) :

```
bit import bit.envs/compilers/react --compiler 
```

您可以构建自己的组件。

```
bit build 
```

现在，声明这是 1.0.0 版本。

```
bit tag --all 1.0.1 
```

再次运行 bit status 以确认是否附加了此版本:

[![](img/bd84e4ad7c067c9bb80ef81c6f286dca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hh-3PXNx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/702/1%2AB2MFyifYN3erAFX2f4SNZg.png)

将它们推到 Bit 远程作用域:

```
bit export kriss.reactcomponent 
```

就是这样！您刚刚导出的组件现在将显示在您的集合中。

[![](img/859fce1963cd082cf5b125da93f9ccb7.png)](https://bitsrc.io/kriss/reactcomponent/components/tabs)

组件定义上面的任何注释块都被用作组件描述，如果我要添加测试，Bit 也会运行它们。

[组件/标签-反应组件位](https://bitsrc.io/kriss/reactcomponent/components/tabs)

### 结论

您已经为自己创建了一个很好的选项卡式组件，并与 Bit 共享。现在，您和您的团队可以在任何您喜欢的应用程序中使用它，以加快构建速度。希望你喜欢，并请随时评论和询问任何事情！干杯😃

### 了解更多

*   [在 React 中加快开发的 5 种工具](https://blog.bitsrc.io/5-tools-for-faster-development-in-react-676f134050f2)
*   [2019 年你应该知道的 11 个 React UI 组件库](https://blog.bitsrc.io/11-react-component-libraries-you-should-know-178eb1dd6aa4)
*   【2019 年 11 个 JavaScript 动画库

* * *