# React 中的高阶组件

> 原文：<https://dev.to/matthewbdaly/higher-order-components-in-react-222h>

在过去的几周里，我一直在对我维护的遗留应用程序的主页进行大规模的重建。由于我一直在慢慢地将它过渡到在前端使用 React，所以我使用了它，这是迄今为止我参与的最大的 React 项目。这促使我使用一些以前从未接触过的更高级的 React 技术。我还必须创建一些具有通用功能的不同组件。

React 曾经使用 mixin 来共享公共功能，但现在的共识是[mixin 被认为是有害的](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)，所以它们已经被移除。相反，鼓励开发人员创建高阶组件来包含共享功能。

高阶组件是一个函数，它接受一个 React 组件作为参数，然后返回另一个包装了所提供组件的组件。共享功能是在包装组件中定义的，因此包装组件中定义的任何状态或方法都可以作为道具传递给被包装的组件，如下面这个简单的例子:

```
import React, { Component } from 'react';

export default function hocExample(WrappedComponent) {
  class hocExample extends Component {
    constructor(props) {
      this.state = {
        foo: false
      };
      this.doStuff = this.doStuff.bind(this);
    }
    doStuff() {
      this.setState({
        foo: true
      });
    }
    render() {
      return (
        <WrappedComponent foo={this.state.foo} doStuff={this.doStuff} />
      );
    }
  }
  return hocExample;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经使用 React 有一段时间了，即使您没有编写高阶组件，您也可能使用过。例如，`react-router`中的`withRouter()`就是一个很好的例子，它是构成现有库的一部分的高阶组件。

# 真实世界的例子

我遇到的一个非常常见的用例是处理组件外部的点击。例如，如果您有一个侧边栏或弹出窗口组件，当用户在组件外单击时，通常希望关闭它。因此，值得花时间重构它以使其可重用。

原则上，您可以在任何组件上实现这一点，如下所示:

*   组件应该接受两个属性——一个表示组件是否活动的`active`属性，以及一个在外部点击时调用的`onClickOutside()`属性方法
*   在挂载时，应该在文档中添加一个事件监听器来监听`mousedown`事件，在卸载时应该删除它
*   当事件侦听器被激发时，它应该使用组件上的 ref 来确定 ref 是否包含事件目标。如果是，并且状态是活动的，那么应该调用`onClickOutside()`方法

将它移到更高阶的元件会使一些问题稍微复杂一点，但不是很复杂。我们不容易获得包装组件的引用，所以我不得不求助于使用`ReactDOM.findDOMNode()`来代替，这可能有点危险，因为他们正在谈论取消它。

```
import React, { Component } from 'react';
import { findDOMNode } from 'react-dom';

export default function clicksOutside(WrappedComponent) {
  class clicksOutside extends Component {
    constructor(props) {
      super(props);
      this.setWrapperRef = this.setWrapperRef.bind(this);
      this.handleClickOutside = this.handleClickOutside.bind(this);
    }
    componentDidMount() {
      document.addEventListener('mousedown', this.handleClickOutside);
    }
    componentWillUnmount() {
      document.removeEventListener('mousedown', this.handleClickOutside);
    }
    setWrapperRef(node) {
      this.wrapperRef = node;
    }
    handleClickOutside(event) {
      const {target} = event;
      if (this.wrapperRef && target instanceof Node) {
        const ref = findDOMNode(this.wrapperRef);
        if (ref && !ref.contains(target) && this.props.active === true) {
          this.props.onClickOutside();
        }
      }
    }
    render() {
      return (
        <WrappedComponent {...this.props} ref={this.setWrapperRef} />
      );
    }
  };
  return clicksOutside;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以这样使用它:

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';
import Sidebar from './src/Components/Sidebar';
import clicksOutside from './src/Components/clicksOutside';

const SidebarComponent = clicksOutside(Sidebar);

function handleClickOutside() {
  alert('You have clicked outside');
}

ReactDOM.render(
  <SidebarComponent 
    links={links} 
    active={true} 
    onClickOutside={handleClickOutside} 
  />,
  document.getElementById('root')
); 
```

Enter fullscreen mode Exit fullscreen mode

高阶元件听起来比实际困难得多。实际上，它们实现起来非常简单，但是我不确定[文档](https://reactjs.org/docs/higher-order-components.html)是否是最好的例子，因为它有点复杂。