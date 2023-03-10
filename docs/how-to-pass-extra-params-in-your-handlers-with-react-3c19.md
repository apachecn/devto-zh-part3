# 如何用 React 给你的处理函数传递额外的参数？

> 原文：<https://dev.to/eatsjobs/how-to-pass-extra-params-in-your-handlers-with-react-3c19>

因此，您希望使用 React 呈现一个列表并将额外的数据传递给函数。

React 不会像 Angular 或 Vue 那样用 html 中的额外语法来处理这个问题(这还是 html 吗？).

```
<li *ngFor="let hero of heroes" (click)="onSelect(hero)"> 
```

Enter fullscreen mode Exit fullscreen mode

那么如何在 React 中高效绑定数据呢？

### 方法 0:不要在家里尝试...或办公室

```
 const things = new Array(32).fill(1).map((el, index) => {
  return {
    index,
    some: 'data'
  };
});

// PLEASE DO NOT DO THIS. But if you want to, who cares ¯\_(ツ)_/¯
class Bad extends React.Component {
  constructor(props) {
    super(props);
    this.state = { message: 'Hello!' };
  }

  handle = el => {
    // WOW a function on every render for every element of the list!!
    alert(JSON.stringify(el));
  };

  render() {
    return (
      <ul>
        {things.map((el, index) => {
          return <li onClick={() => this.handle(el)}>{el.some + el.index</li>;
        })}
      </ul>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题如下:**在每次渲染时为列表中的每个元素创建一个函数！！！**

那么这个怎么解决呢？短列表的性能会很好，但是这种方法没有伸缩性。该列表可以是动态的，也可以来自服务呼叫。

### 方法 1:将数据放入 DOM 并在处理程序中获取

```
const things = new Array(32).fill(1).map((el, index) => {
  return {
    index,
    some: 'data'
  };
});
class Approach1 extends React.Component {
  handleClick = evt => {
    // get item stored in dom
    // PRO: single function
    // CONS: dom payload increase, stringify on every call but this is optional
    const item = JSON.parse(evt.target.dataset.item);
    const index = parseInt(evt.target.dataset.index);
    alert(JSON.stringify(item) + index);
  };
  render() {
    return (
      <ul>
        {things.map((el, index) => {
          return (
            <li
              data-item={JSON.stringify(el)}
              data-index={el.index}
              onClick={this.handleClick}
            >
              {el.some + el.index}
            </li>
          );
        })}
      </ul>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法是好的，但是如果你有一个太大的项目模型就不是最优的。
**PRO:列表中每一项的单个函数**
**CONS:如果你有庞大的数据模型，dom 的有效负载会增加**

### 方法二:最佳(？)和冗长的方法

```
const things = new Array(32).fill(1).map((el, index) => {
  return {
    index,
    some: 'data'
  };
});
class BestApproach extends React.Component {
  constructor(props) {
    super(props);
    this.state = { message: 'Hello!' };
    this.handlers = new Map();
  }
  getHandler = (el, index) => {
    if (!this.handlers.has(el)) {
      this.handlers.set(el, evt => {
        // do somenthing with the event
        alert(JSON.stringify(el) + index);
      });
    }
    return this.handlers.get(el);
  };

  componentWillMount() {
    this.handlers.clear();
  }
  render() {
    return (
      <ul>
        {things.map((el, index) => (<li onClick={this.getHandler(el, index)}>{el.some + el.index}</li>))}
      </ul>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我们为每个项目创建了一个函数，但是只在第一次渲染的时候。下次我们从 getHandler 取回缓存的函数时。

### 方法三:事件委托。可能是最好的一个

```
const things = new Array(32).fill(1).map((el, index) => {
  return {
    index,
    some: 'data'
  };
});

class MyLi extends React.Component {
  _handler = (evt) => {
    const { item, onPress } = this.props;
    onPress(item, evt);
  }
  render() {
    return <li onClick={this._handler}>{this.props.children}</li>;
  }
}
class BestOfBestApproach extends React.Component {
  constructor(props) {
    super(props);
    this.state = { message: 'Hello!' };
    this.handlers = new Map();
  }
  handler = ({ index, some }) => {
    alert(some);
  };

  render() {
    return (<ul>
      {things.map((el, index) => (<MyLi onPress={this.handler}>{el.some + el.index}</MyLi>))}
    </ul>);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/react-cache-handlers-585c9](https://codesandbox.io/embed/react-cache-handlers-585c9)

如果您有更好的解决方案/方法，或者看到一些错误，请发表评论。

再见，快乐的 React 编码！