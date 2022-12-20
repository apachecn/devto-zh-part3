# React 中的高级视图和逻辑分离

> 原文：<https://dev.to/tomekbuszewski/high-level-view-and-logic-separation-in-react-39n0>

React 最大的优势之一是能够将视图与逻辑分开。我想更进一步，创建两个独立的*组件*，一个包含视图，一个是逻辑。

乍一看，这可能听起来像一个开销。*为什么我要创建两个文件，我可以使用`render`方法。*虽然这是真的，但这种方法有很多好处。首先，它允许两个人同时做一件事。一个是初级人员，可以编写样式和 JSX 标记，而一个更有经验的开发人员可以编写其逻辑处理。另一个有效的观点是关注点的分离——一个完整的视图可以被替换，甚至不用接触内部有逻辑的文件，只要它们使用相同的道具。

那么，我该如何处理这样的事情呢？很简单，真的。

首先，我定义了国家。组件应该对鼠标悬停做出反应，区分其活动和休眠状态，并显示传递给它的文本。接下来，我写逻辑。大多数情况下，这是一个具有一些内部方法的类:

```
// containers/Thing/index.js

import View from "@src/ui/Thing";

class Thing extends React.Component {
  state = {
    active: false,
    hovered: false,
  };

  setActiveState = () => this.setState({ active: !this.state.active });

  setHoveredState = () => this.setState({ hovered: !this.state.hovered });

  render() {
    return (
      <View
        active={this.state.active}
        hovered={this.state.hovered}
        onClick={this.setActiveState}
        onMouseEnter={this.setHoveredState}
        onMouseLeave={this.setHoveredState}
        text={this.props.text}
      />
    );
  }
} 
```

逻辑到此为止。当然，还可以有更多，但为了便于说明，这就足够了。

接下来，查看。

```
// ui/Thing/index.js

const Thing = (props) => {
  const {
    active,
    hovered,
    onClick,
    onMouseEnter,
    onMouseLeave,
    text,
  } = props;

  return (
    <div
      onClick={onClick}
      onMouseEnter={onMouseEnter}
      onMouseLeave={onMouseLeave}
      style={{
        background: active ? "tomato" : "transparent",
        color: hovered ? "yellow" : "black",
      }}
    >
      {text}
    </div>
  );
}; 
```

又来了，就是这个。非常简洁明了。

这种模式在有初级和中级开发人员的团队中非常有效，因为他们可以致力于目标，而不会因为缺乏更复杂的逻辑知识或对编写视图缺乏兴趣而留下来。

—

*   一个最小的例子可以在 [CodeSandbox](https://codesandbox.io/s/3y7nl6z7mm) 上看到；
*   这个架构的完整例子可以在[我的项目](https://github.com/tomekbuszewski/Videoplayer)中找到。