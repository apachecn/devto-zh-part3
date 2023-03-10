# 在 5 分钟内创建一个包含所有反应生命周期方法的 Wordpress Gutenberg-Block

> 原文：<https://dev.to/martinkr/create-a-wordpress-s-gutenberg-block-with-all-react-lifecycle-methods-in-5-minutes-213p>

几周前，我在创作一个定制的古腾堡积木。我需要查询一个 API 来为编辑器提供实时数据。
由于 WordPress Gutenberg-Blocks 是建立在`React`之上的，我想使用`componentDidMount`和随后的完整的`React`-生命周期。
最后，我只需要做一些小的修改，就可以在我的古腾堡模块中获得完整的`React`功能。

让我向你展示释放 React 力量的最快方法。

## 序幕:古腾堡&古腾堡-布洛克斯

从 WordPress 5.0 开始，古腾堡“只是新的编辑器”。唉，这意味着重新定义 WordPress 的完整出版体验。

不同于当前 WordPress 页面的定制 HTML、“WordPress-shortcodes”和“magic embeds”的粗糙混合，每个元素都将是一个“古腾堡块”。

一个“古腾堡块”包含一个特征，一个交互，并提供一个单一的用户界面。用户不需要任何技术知识来创建一个页面与古腾堡块。

古腾堡已经自带了一套基本块，如`paragraph`、`image`、`blockquote`或`recent blocks`等。你可以用 JavaScript，HTML，CSS 和 PHP 创建自定义块。

## 创建古腾堡-街区

Gutenberg 使用基于 React 的现代前端堆栈。基础是`wp.element`，它是位于`React`之上的一个很薄的抽象层。Gutenberg 用它来创建块和静态 HTML 来保存内容。“编辑器-视图”使用 React 组件来保存静态的、序列化的 HTML。“访问者”接收保存的静态 HTML，而不是 React 组件。

创建自定义古腾堡块的最简单方法是使用样板文件。我来给你介绍一下 [create-guten-block](https://github.com/ahmadawais/create-guten-block) 。

> `create-guten-block`是*zero configuration dev-toolkit*(# 0CJS)在几分钟内开发 WordPress Gutenberg blocks，无需配置`React`、`webpack`、`ES6/7/8/Next`、`ESLint`、`Babel`等。

### …同`create-guten-block`

让我们创建一个简单的“样板”Gutenberg-Block，我们将修改它以使用期望的 React-lifecycle 方法。

打开您的终端，通过键入:
从`create-guten-block`创建样板代码

```
$ cd wp-content/plugins/
$ npx create-guten-block React-lifecycle-block
$ cd React-lifecycle-block
$ npm start 
```

Enter fullscreen mode Exit fullscreen mode

## 将其转换成`React.Component`

现在，我们需要做两处修改:

### 导入`React.Component`

[`wp.element`](https://github.com/WordPress/gutenberg/tree/master/packages/element) 是古腾堡在`React`上面薄薄的抽象层。它提供了一个 API 来利用任何定制古腾堡块中的大多数`React's`功能。

首先，从文件
顶部的`wp.element`导入`React.Component`

```
const { Component } = wp.element; 
```

Enter fullscreen mode Exit fullscreen mode

### 使用`React.Component`

现在，我们使用 Com
将`edit`函数转换成`React.Component`

```
edit: class extends Component { … } 
```

Enter fullscreen mode Exit fullscreen mode

添加`constructor`-功能…

```
//standard constructor for a React.Component
constructor() {
    super(...arguments);
    console.log(this.props.name, ": constructor()");
    // example how to bind `this` to the current component for our callbacks
    this.onChangeContent = this.onChangeContent.bind(this);
    // a place for the state
    this.state = {};
} 
```

Enter fullscreen mode Exit fullscreen mode

…以及期望的生命周期功能到`edit`-功能…

```
// componentDidMount() is invoked immediately after a component is mounted
// https://Reactjs.org/docs/React-component.html#componentdidmount
componentDidMount() {
    console.log(this.props.name, ": componentDidMount()");
}
// componentDidUpdate() is invoked immediately after updating occurs
// https://Reactjs.org/docs/React-component.html#componentdidmount
componentDidUpdate() {
    console.log(this.props.name, ": componentDidUpdate()");
}
// componentWillUnmount() is invoked immediately before a component is unmounted and destroyed
// https://Reactjs.org/docs/React-component.html#componentdidmount
componentWillUnmount() {
    console.log(this.props.name, ": componentWillUnmount()");
} 
```

Enter fullscreen mode Exit fullscreen mode

…最后，我们需要在从`edit`函数返回 HTML 字符串之前调用`render`。

```
render() {
    return (<div className={this.props.className}></div>); } 
```

Enter fullscreen mode Exit fullscreen mode

**就这样。**

## 源代码

*   样板代码来自`create-guten-block`