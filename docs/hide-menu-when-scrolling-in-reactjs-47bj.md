# 在 React.js 中滚动时隐藏菜单

> 原文：<https://dev.to/guimg/hide-menu-when-scrolling-in-reactjs-47bj>

在本教程中，我将解释如何制作一个当我们滚动页面时隐藏或显示的*导航条*。这是 **React.js** 的一个版本，它使用组件的*状态*来随时了解我们的导航条的当前状态。

## 组件

现在，我们将了解我们的组件需要哪些部件。首先，正如我们所说的，我们将在我们的*状态*中保存滚动的位置，我们将在`constructor()`中为*状态*创建一个新值，该值将取页面偏移量的初始值。

当然，我们还需要`render()`方法来返回一个包含所有导航条项目的`nav`。这里先来看看:

```
import React, { Component } from "react";
import classnames from "classnames";

export default class Navbar extends Component {
  constructor(props) {
    super(props);

    this.state = {
      prevScrollpos: window.pageYOffset,
      visible: true
    };
  }

  render() {
    return (
      <nav
        className={classnames("navbar", {
          "navbar--hidden": !this.state.visible
        })}
      >
        <a href="#">Item 1</a>
        <a href="#">Item 2</a>
        <a href="#">Item 3</a>
      </nav>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是 CSS:

```
.navbar {
  width: 100%;
  padding: 10px;
  position: fixed;
  top: 0;
  transition: top 0.6s;
}

.navbar--hidden {
  top: -50px;
} 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们的组件已经可以在浏览器中查看了，但是还没有我们想要的行为。让我们去吧！

首先，我们需要实现隐藏或显示导航栏的功能。它将像一个事件一样被调用。它将根据我们是向上还是向下滚动来判断当前的偏移量是大于还是小于先前的偏移量。如果偏移较大，我们就上升，因此会显示菜单。相反，它会把它藏起来。这种显示/隐藏行为由**可见的**状态变量管理。

```
handleScroll = () => {
  const { prevScrollpos } = this.state;

  const currentScrollPos = window.pageYOffset;
  const visible = prevScrollpos > currentScrollPos;

  this.setState({
    prevScrollpos: currentScrollPos,
    visible
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在功能完成了。但是我们需要在用户每次滚动屏幕时调用它。我们将使用*生命周期方法*在滚动中添加和删除监听器。

```
componentDidMount() {
  window.addEventListener("scroll", this.handleScroll);
}

componentWillUnmount() {
  window.removeEventListener("scroll", this.handleScroll);
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们就完成了我们的部分。接下来，我展示所有的完整代码。我希望你喜欢它，我会更经常上传内容。回头见！

```
import React, { Component } from "react";
import classnames from "classnames";

export default class Navbar extends Component {
  constructor(props) {
    super(props);

    this.state = {
      prevScrollpos: window.pageYOffset,
      visible: true
    };
  }

  // Adds an event listener when the component is mount.
  componentDidMount() {
    window.addEventListener("scroll", this.handleScroll);
  }

  // Remove the event listener when the component is unmount.
  componentWillUnmount() {
    window.removeEventListener("scroll", this.handleScroll);
  }

  // Hide or show the menu.
  handleScroll = () => {
    const { prevScrollpos } = this.state;

    const currentScrollPos = window.pageYOffset;
    const visible = prevScrollpos > currentScrollPos;

    this.setState({
      prevScrollpos: currentScrollPos,
      visible
    });
  };

  render() {
    return (
      <nav
        className={classnames("navbar", {
          "navbar--hidden": !this.state.visible
        })}
      >
        <a href="#">Item 1</a>
        <a href="#">Item 2</a>
        <a href="#">Item 3</a>
      </nav>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode