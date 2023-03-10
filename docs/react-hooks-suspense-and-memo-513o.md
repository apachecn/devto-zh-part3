# 反应钩、悬念和备忘录

> 原文：<https://dev.to/whoisryosuke/react-hooks-suspense-and-memo-513o>

> 编者注:我写这篇文章的时候，Hooks 还在 alpha 版本(React v16.6)，现在 Hooks 正式发布了(v16.8)，我决定写完这篇文章。

最近 React 社区的事情越来越多了！在几个月的*悬念*之间，Create React App v2、Hooks、Memo - React 的新老开发者们都忙着玩新玩具。我终于有时间深入研究新的`React.memo()`、`React.lazy()`和`<Suspense />`API，以及提议的 Hooks API。

## PureComponent 为功能组件

一种新的记忆技术！`React.memo()`是一个特设，防止一个组件在道具相同的情况下改变道具。它基本上在`shouldComponentUpdate()`生命周期中的 props 上运行一个浅层 equal，但是对于不能访问它的功能组件(没有切换到类)。

```
const MyComponent = React.memo(function MyComponent(props) {
  /* render using props */
}); 
```

Enter fullscreen mode Exit fullscreen mode

而如果道具中包含复杂的物体，我们可以在组件内部添加一个函数来检查:

```
function MyComponent(props) {
  /* render using props */
}
function areEqual(prevProps, nextProps) {
  /*
  return true if passing nextProps to render would return
  the same result as passing prevProps to render,
  otherwise return false
  */
}
export default React.memo(MyComponent, areEqual); 
```

Enter fullscreen mode Exit fullscreen mode

对于依赖功能组件来呈现低级 UI 元素的组件和设计系统来说，这是一个巨大的性能增益。

### 回调“缓存”

还实现了一个新的钩子，它在函数上使用相同的记忆逻辑。它防止函数被再次调用，除非它的参数(或你指定的变量)改变:

```
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]); 
```

Enter fullscreen mode Exit fullscreen mode

## 悬念结束了🌟

我首先想挖掘的是悬念，因为它实际上已经实现了(如果不是不完整的话)。在 3 月观看了 Dan 在 ReactFest 2018 上关于悬念的[令人难以置信的演讲后，我很高兴 React 将延迟加载作为一个优先事项，足以将其纳入他们的 API。我不需要依赖像](https://www.youtube.com/watch?v=6g3g0Q_XVb4)[这样的库或者 Webpack 中的配置，我只需要:](https://github.com/jamiebuilds/react-loadable#readme) 

```
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我不仅获得了延迟加载我的组件包的好处(使应用程序最初加载更快)，而且我还可以插入任何加载组件。这使得像*骨架屏幕*这样的幻觉变得轻而易举。

你可以在 CodeSandbox 上看到[一个活生生的例子:](https://codesandbox.io/s/y2453lj5xj?view=editor)

## 挂钩

最近，React 提出了一种新的、功能更强的方法，使用“钩子”来处理状态，而不是依赖 React 组件的生命周期方法。您可以在此处的 React 文档中找到[整个提案。](https://reactjs.org/docs/hooks-intro.html)

使用它们很简单，与同类产品相比，功能组件的 LOC 更低。

```
function YourComponent({ text }) {
  const [ theText, updateText] = useState(text)
  const changeText = ({ target: { value } }) => {
    updateText(value)
  }
  return(
    <button onClick={() => changeText}>
      {theText}
    </button>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

为了处理组件中的任何副作用，在功能组件中加入一个`useEffect()`来在每次状态改变/重新呈现时运行代码。

钩子最好的部分之一是它们的功能特性(FP FTW)。您可以将挂钩和效果提取到一个单独的函数中，并在应用程序的多个组件中重用该挂钩。

### 钩子=较少编译的代码

增加钩子最好的地方之一是能够放弃有状态逻辑的类，支持更有效的函数。如果你曾经看过大多数编译过的 JS 代码，由于类的工作方式(相对于原型是语法上的糖衣)，在你的应用中使用一个类会用 polyfills 极大地膨胀你的代码。

本课:

```
class Test extends React {
  constructor() {
    super()
    this.state = {}
  }
  render() {
    return <div>Test</div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

编译到:

```
"use strict";

var _createClass = function () { function defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ("value" in descriptor) descriptor.writable = true; Object.defineProperty(target, descriptor.key, descriptor); } } return function (Constructor, protoProps, staticProps) { if (protoProps) defineProperties(Constructor.prototype, protoProps); if (staticProps) defineProperties(Constructor, staticProps); return Constructor; }; }();

function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

function _possibleConstructorReturn(self, call) { if (!self) { throw new ReferenceError("this hasn't been initialised - super() hasn't been called"); } return call && (typeof call === "object" || typeof call === "function") ? call : self; }

function _inherits(subClass, superClass) { if (typeof superClass !== "function" && superClass !== null) { throw new TypeError("Super expression must either be null or a function, not " + typeof superClass); } subClass.prototype = Object.create(superClass && superClass.prototype, { constructor: { value: subClass, enumerable: false, writable: true, configurable: true } }); if (superClass) Object.setPrototypeOf ? Object.setPrototypeOf(subClass, superClass) : subClass.__proto__ = superClass; }

var Test = function (_React) {
  _inherits(Test, _React);

  function Test() {
    _classCallCheck(this, Test);

    var _this = _possibleConstructorReturn(this, (Test.__proto__ || Object.getPrototypeOf(Test)).call(this));

    _this.state = {};
    return _this;
  }

  _createClass(Test, [{
    key: "render",
    value: function render() {
      return React.createElement(
        "div",
        null,
        "Test"
      );
    }
  }]);

  return Test;
}(React); 
```

Enter fullscreen mode Exit fullscreen mode

相比之下，如果你使用一个函数(除非它是 ES6 arrow 函数)，它就像它出现时一样编译——因为函数被如此广泛地支持(如此原始/早期的 JS API)。即使考虑到数组析构，[代码仍然比类](https://babeljs.io/repl/#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=GYVwdgxgLglg9mABAFQKYGcoAoAOAnOHdASkQG8AoRRCBTRAbVvClTwBpEZI9UBbVGCgBdRAF5EIdKgDKUAIassABmIBuKol5QQeJAB4AFgEYAfAAlUAGytx9AehOmNAXyA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=false&targets=&version=6.26.0&envVersion=)少，同时能够使用状态:

```
function Test(props) {
  const [counter, increment] = useState(0);
  return <h1>Hello</h1>; } 
```

Enter fullscreen mode Exit fullscreen mode

```
"use strict";

var _slicedToArray = function () { function sliceIterator(arr, i) { var _arr = []; var _n = true; var _d = false; var _e = undefined; try { for (var _i = arr[Symbol.iterator](), _s; !(_n = (_s = _i.next()).done); _n = true) { _arr.push(_s.value); if (i && _arr.length === i) break; } } catch (err) { _d = true; _e = err; } finally { try { if (!_n && _i["return"]) _i["return"](); } finally { if (_d) throw _e; } } return _arr; } return function (arr, i) { if (Array.isArray(arr)) { return arr; } else if (Symbol.iterator in Object(arr)) { return sliceIterator(arr, i); } else { throw new TypeError("Invalid attempt to destructure non-iterable instance"); } }; }();

function Test(props) {
  var _useState = useState(0),
      _useState2 = _slicedToArray(_useState, 2),
      counter = _useState2[0],
      increment = _useState2[1];

  return React.createElement(
    "h1",
    null,
    "Hello"
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

## React 的更可组合的未来

很高兴看到 React API 在过去一年中的改进。该团队在维护传统 API 和不破坏应用程序方面做得非常出色(脸书仍在使用`React.createElement`),新功能的添加都解决了开发人员的关键问题。我不知道有多少次，我不得不把一个功能组件转换成一个类，仅仅是为了一个有状态的布尔值，现在我只需要在函数的顶部放一个钩子(并对它进行记忆，以获得与 PureComponent 相同的性能！).

Cheers 🍻
Ryo

* * *

**参考文献**:

*   [令人敬畏的反应钩子](https://github.com/rehooks/awesome-react-hooks)
*   [挂钩导轨](https://www.hooks.guide/)
*   [丹·阿布拉莫夫——理解钩子](https://dev.to/dan_abramov/making-sense-of-react-hooks-2eib)
*   [反应:CRA v2](https://reactjs.org/blog/2018/10/01/create-react-app-v2.html)
*   [反应:钩子介绍](https://reactjs.org/docs/hooks-intro.html)
*   [反应:代码拆分](https://reactjs.org/docs/code-splitting.html)
*   [反应:悬念](https://reactjs.org/docs/react-api.html#suspense)
*   [反应:备忘录](https://reactjs.org/docs/react-api.html#reactmemo)