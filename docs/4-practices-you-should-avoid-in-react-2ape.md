# React 中应避免的 4 种做法

> 原文：<https://dev.to/ixartz/4-practices-you-should-avoid-in-react-2ape>

最近，我在工作和个人项目中频繁使用 React。在这里，我将分享我在 React 代码中所犯的错误。还有，在你的项目中你应该避免做什么。

您可以在这个位置使用 React [访问我的一个个人项目。我在这里列出的 4 个错误是在我实现数字识别器的项目中出现的。这个项目帮助我学习 Redux、Tensorflow.js、styled-components、Ant Design 等。我很高兴与 React 分享我在这个小型深度学习项目中所学到的东西。](https://github.com/ixartz/handwritten-digit-recognition-tensorflowjs)

### [渲染功能中的](#arrow-function-in-the-render-function)箭头功能

你应该避免的第一件事是在 React 的渲染函数中内嵌一个箭头函数。ESLint 规则是 *react/jsx-no-bind* 。这里有一个例子:

```
class Button extends React.Component {
  render() {
    return (
      <button onClick={() => { console.log("Hello world!"); }}>
        Click me!
      </button>
    );
  }
} 
```

前面的代码有什么问题？这个函数会在每次父组件渲染时重新创建。正如您所猜测的，这将在两个方面损害应用程序的性能。首先，它会在每次呈现父组件时创建一个不必要的匿名函数。

然后，它创建一个新的匿名函数，React 也会触发子组件的重新呈现。这会打破*的反应。PureComponent* 或 *shouldComponentUpdate* 优化。

#### 解

这很容易解决，你不应该在渲染中声明你的箭头函数。您应该将箭头函数作为类字段移动。然后，子组件 props 应该引用这个类字段。这里有一个解决方案:

```
class Button extends React.Component {
  handleClick = () => {
    console.log("Hello world!");
  };

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me!
      </button>
    );
  }
} 
```

#### 越走越深

在改变你所有的内联函数之前，你还应该阅读这两篇文章:

*   [反应、内联函数和性能](https://cdb.reacttraining.com/react-inline-functions-and-performance-bdff784f5578)
*   [是否有必要应用 ESLint jsx-no-bind 规则？](http://shzhangji.com/blog/2018/09/13/is-it-necessary-to-apply-eslint-jsx-no-bind-rule/)

他们认为 *react/jsx-no-bind* 是一个不成熟的优化。我会让你自己思考这个话题。

### 嵌套状态

我在 React 中尝试使用嵌套状态时犯了一个错误。嵌套状态是将一个对象放入 React 的状态。例如，下面的代码是一个嵌套状态:

```
let coord = {
  x: 0,
  y: 0,
  width: 200,
  height: 200
};

this.state = {
  coord
}; 
```

当您尝试更新*坐标*对象时，会出现嵌套状态问题:

```
coord.x = 10;

this.setState({
  coord
}); 
```

您正在期待组件被再次呈现。不幸的是， *PureComponent* 的情况并非如此。React 对组件状态进行简单的比较，它看不到状态的变化。

使用嵌套状态时需要注意的另一件事是 stateState 执行浅层合并。

```
constructor() {
  this.state = {
    x: 10,
    y: 10
  };
}

otherfunction() {
  this.setState({
    y: 100
  });
} 
```

你期待 **this.state.x = 10** 和 **this.state.y = 100** 。但是，当您有一个嵌套状态时，例如:

```
constructor() {
  this.state = {
    coord: {
      x: 10,
      y: 10
    }
  };
}

otherfunction() {
  this.setState({
    coord: {
      y: 100
    }
  });
} 
```

**this.state.coord.x** 会变成*未定义*。

#### 解

根据您的具体情况，以下是您可以遵循的解决方案:

1.  只要改变你的设计，避免使用嵌套状态
2.  使用析构，它将取消嵌套你的对象到状态
3.  当您进行变更时，也可以自己创建一个新对象。但是，我建议使用一个不可变的库。脸书提供了 *Immutable.js* ，它将完成这项工作。

每个解决方案都有自己的优点和缺点。您应该根据自己的情况选择解决方案。

### 用条件渲染显示/隐藏组件

您可能知道，React 允许您基于条件渲染组件。我想我可以利用这种条件渲染来显示/隐藏组件。实际上，您应该使用条件渲染来切换小组件。

但是，对于复杂的问题，您应该避免。尤其是当你有一个复杂的*构造器*或者一个复杂的安装过程的时候。即使它工作得很好，但在幕后，每次我们显示/隐藏元素时，组件都被不必要地重新创建。

```
class Button extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      show: true
    };
  }

  handleClick = () => {
    this.setState({
      show: !this.state.show
    });
  };

  render() {
    return (
      <div>
        <button onClick={this.handleClick}>
          Click me!
        </button>
        {/* Here is the conditional rendering */}
        {this.state.show && <ComplexComponent />}
      </div>
    );
  }
} 
```

上面的代码会在你每次点击按钮时切换 *ComplexComponent* 组件。每次点击隐藏/显示 *ComplexComponent* 组件非常有效。但是，有一个主要的缺点:每次我们显示回 *ComplexComponent* 组件时，它会实例化一个新的实例，并从头开始重新创建一个新的实例。

您应该避免使用条件呈现。特别是当 *ComplexComponent* 组件有消耗资源的构造器和/或挂载过程时。事实上，每次我们显示组件时，都会调用*构造函数*和 *componentDidMount* 方法。

#### 解

React 显示或隐藏组件的另一种方式是使用 CSS。一个简单的 *display* CSS 属性可以用来显示/隐藏一个组件，而不需要重新创建它。

下面，您可以找到一个可以应用 *display* CSS 属性的示例:

```
.hidden {
  display: none;
} 
```

```
render() {
  const classname = this.state.show ? null : 'hidden';

  return (
    <div>
      <button onClick={this.handleClick}>
        Click me!
      </button>
      {/* Here is the conditional rendering */}
      <ComplexComponent className={classname} />
    </div>
  );
} 
```

#### 警告

不要在 React 应用程序中滥用*显示*规则。使用 *display: none* ，React 仍然会呈现元素并添加到 DOM 中。请使用这两种解决方案根据您的上下文切换组件。

### target="_blank "安全

它不仅与 React 应用程序相关。但是，我是在 React 项目中学习的。多亏了 ESLint，它发出了 *react/jsx-no-bind* 警告，我发现这个简单的代码存在安全问题:

```
<a href="http://malicious-website.com" target="_blank">Click here!</a> 
```

我无法想象你网站上的这一行代码会给你的应用程序带来漏洞。

攻击者可以将以下代码放在他的恶意网站上:

```
window.opener.location = "http://fake-facebook.com"; 
```

它可以重定向你的网站显示到任何网站的标签。

#### 解

在您的链接中，您只需要添加 *rel="noopener noreferrer"* ，您应该会得到以下代码:

```
<a href="http://malicious-website.com" target="_blank" rel="noopener noreferrer">Click here!</a> 
```

现在，您在这个安全问题上是安全的。

## 结论

以下是我在 React 工作时犯的 4 个错误。我在继续学习，但我希望你能避免犯和我一样的错误。如果你也有一些其他的反模式，不要犹豫，在下面留下评论。如果你喜欢这篇文章，我会在 React 中分享更多不好的做法。