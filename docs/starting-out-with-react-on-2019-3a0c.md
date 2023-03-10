# 从用钩子反应开始

> 原文：<https://dev.to/frontendwizard/starting-out-with-react-on-2019-3a0c>

你好。

几周前，React 正式发布了这个名为 Hooks 的新 API，过去几个月一直好奇的看着 React，甚至已经编写了一些组件的您，一直想知道现在是否是时候学习更多关于 React 的知识了...

好吧，如果有一件事我可以向你保证，那就是现在，比以往任何时候都更是开始培养你的反应技能的好时机。React 已经存在了 5 年，从它的环境和过去的经验中学到了很多，它是一个坚如磐石的库，可以在任何地方构建应用程序。这个新的 API 使得在组件之间组合和使用逻辑变得更加简洁和容易。

让我们简单介绍一下最原始的概念，介绍一下最基本的钩子，然后我会给你指出一些很棒的资源，在那里你可以学到关于 React 的一切。

# 有什么反应？

你可能知道此时会有什么反应，但是让我们先把这个弄清楚。React 是一个库，处理寻找最佳方式来呈现你的应用程序。它是用 JavaScript 构建的，我们主要用它来构建 web 应用程序，但它现在几乎可以用于任何地方的渲染，比如在 Android 和 iOS 上，用 React Native，在电视上，在服务器上，在命令行上，等等。

这怎么可能呢？React 构建了逻辑，以找到与实际渲染图元分离的渲染所需的最小工作量。这样，要在某个地方使用 React，我们需要做的就是构建渲染层。对于网络来说，这就是`react-dom`库。

要创建一个 React 元素，我们需要做的就是将 React 和 ReactDOM 导入到一个 html 文件中，然后在前面两个标签之后打开第三个脚本标签，开始创建组件，对吗？

```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  React Example
</head>

<body>
  <div id="root"></div>
  <script src="https://unpkg.com/react@16.8.3/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.8.3/umd/react-dom.development.js"></script>
  <script>
    // Your code is going to go here
  </script>
</body>

</html> 
```

嗯，不完全是。我们将在那里添加我们的根 div，这将是我们呈现组件的地方，但是首先我们必须创建我们的组件，然后我们必须将它们放入我们创建的`root` div 中。

为了创建一个元素，我们必须在第三个标签中做以下事情:

```
const App = () => React.createElement(
    'div',
    {},
    React.createElement('h1', {}, 'My Element')
) 
```

为了渲染它，我们在应用程序组件后添加:

```
ReactDOM.render(React.createElement(App), document.getElementById("root")) 
```

现在我们应该看到`div`中的`h1`元素，里面有“My Element”文本。

我知道，你会说:“真的吗？所有这些都是为了呈现一些简单的 html？”。再多陪我一会儿。

我们应该注意，React 渲染的所有东西都是组件。这是 React 的又一大胜利。你的应用被分割成小块，这些小块一起工作，并抽象出它的内部结构。

我们在这里使用了`h1`和`div`，但是你放在那里的任何东西都将是输出，所以你也可以使用 web 组件。

我们也可以随心所欲地嵌套组件，这就是事情开始变得有趣的时候。

让我们创建一个`Person`组件。

```
const Person = () => React.createElement(
    'div',
    {},
    [
        React.createElement('h1', {}, 'Juliano Rafael'),
        React.createElement('h2', {}, '@frontendwizard'),
        React.createElement('h2', {}, 'Front End Developer'),
    ]
) 
```

现在，在我们的应用程序中，我们可以创建尽可能多的`Person`组件实例:

```
const App = () => React.createElement(
    'div',
    {},
    [
        React.createElement(Person),
        React.createElement(Person),
        React.createElement(Person),
    ]
) 
```

有点无聊，但是很有趣。你应该在你的 html 上看到三个胡利亚诺。胡利亚诺病太严重了，没人会满意的😄。组件的值被硬编码到组件中。让我们用最基本的组件特性`props`来改变它。你注意到我们作为`React.createElement`的第二个参数不断传入的空对象了吗？那些就是`props`。它们保留传递给对象的所有属性。方便的是，它们也是传递给函数组件的第一个参数。让我们改变`Person`组件来使用它。

```
const Person = props => React.createElement(
    'div',
    {},
    [
        React.createElement('h1', {}, props.name),
        React.createElement('h2', {}, props.twitter),
        React.createElement('h2', {}, props.job),
    ]
) 
```

酷，现在我们可以摆脱那些胡利亚诺的克隆，给每个实例它自己的身份。

```
const App = () => React.createElement(
    'div',
    {},
    [
        React.createElement(Person, {
            name: "Juliano Rafael",
            twitter: "@frontendwizard",
            job: "Front End Developer"
        }),
        React.createElement(Person, {
            name: "Leonardo Elias",
            twitter: "@leonardoelias_",
            job: "Front End Developer"
        }),
        React.createElement(Person, {
            name: "Marcos Eptacio",
            twitter: "@eptaccio",
            job: "JavaScript Developer"
        }),
        React.createElement(Person, {
            name: "Wallace Batista",
            twitter: "@uselessdevelop",
            job: "Front End Developer"
        }),
    ]
) 
```

很酷，对吧？现在我们可以征服世界，构建最复杂的应用程序！当然不是用这种语法。虽然我们已经可以将代码抽象成组件，这很好，但是这种语法太麻烦了，难以阅读。是时候在此基础上发展了。

# JSX

没有 JSX 就没有人使用 React 是有原因的，因为 JSX 大大提高了可读性。JSX 让你写一个类似 HTML 的语法，它可以被翻译成我们目前所做的。不利的一面是，现在我们需要在我们的工作流程中有这个传输过程来使用它。我们可以直接在浏览器中使用`@babel/standalone`来实现这一点，但是这对生产没有好处，因为它实时传输代码，这是在浏览器中处理代码之前必须要做的一个步骤。

今天在 React 项目中使用的头号构建工具是`webpack`。多亏了`create-react-app`，整个设置在`react-scripts`中被抽象出来。你需要做的就是:

```
npx create-react-app my-app
cd my-app
npm start 
```

这将在`http://localhost:3000`启动一个全新的应用程序。

现在你对我说:“嘿，你是如何从简单的 html 文件和脚本到复杂的 webpack 设置的，这对我来说就像魔术一样？”

我听到了。相信我，你会喜欢这个的。在这一点上，你需要知道的是，这种设置为你提供了一个可靠的工作流程来开发、调试和发布你的应用程序，而不需要连接所有的 webpack 配置，这可能是超级压倒性的。这个设置将把你的 JSX 代码向下转换到我们之前所做的，并允许我们使用 ES2015+中一些并非在所有浏览器中都可用的好功能。最后，相信`react-scripts`是一个安全的赌注，会让你内心平静。

设置完毕，现在我们可以重构我们的`Person`组件来使用 JSX:

```
const Person = props => (
    <div>
        <h1>{props.name}</h1>
        <h2>{props.twitter}</h2>
        <h2>{props.job}</h2>
    </div>
) 
```

而`App`可以这样写:

```
const App = () => (
    <div>
        <Person
            name="Juliano Rafael"
            twitter="@frontendwizard"
            job="Front End Developer"
        />
        <Person
            name="Leonardo Elias"
            twitter="@leonardoelias_"
            job="Front End Developer"
        />
        <Person
            name="Marcos Eptacio"
            twitter="@eptaccio"
            job="JavaScript Developer"
        />
        <Person
            name="Wallace Batista"
            twitter="@uselessdevelop"
            job="Front End Developer"
        />
    </div>
) 
```

我不知道你怎么想，但我觉得这样好多了。嵌套组件越多，就越能看到 JSX 的好处。

这里有一些关于 JSX 的小细节，你可以在这里阅读关于 JSX 的所有细节。现在，你只需要知道:

*   您的组件，除了 HTML 中指定的组件，*必须大写*，否则它会试图将它作为 web 组件而不是 React 组件。
*   如果你要给你的组件添加一个类，你需要使用`className`属性，而不是`class`，因为 class 是 JavaScript 的保留字。
*   所有带连字符的属性都需要以`data-`为前缀，否则 React 不会在 DOM 上显示它们。

我可以接受。

# 将你的组件拆分成多个文件

你可能已经注意到，使用`create-react-app`，默认情况下组件被分割成单独的文件，并使用 ES2015 语法导入和导出。

这是可能的，因为 webpack 设置在幕后。这样，你就可以把每个组件放入它自己的文件中，并通过使用:
把它导出

```
export default MyComponent 
```

在另一个文件上，您可以使用 import 语句导入组件:

```
import MyComponent from './relative-path-to-MyComponent' 
```

* * *

好了，现在我们可以用类似 HTML 的语法来编写组件，把它分解成更小的组件，抽象出它的实现，但所有这些仍然是静态的。这是因为道具不能在组件内部改变，它们是不可变的。我们需要一些东西，我们可以在其中存储一些状态并动态地改变它，同时让 React 更新接口作为响应。

让我们谈谈国家。

# 状态

状态是组件的动态属性。在组件的生命周期中，您可以随意更改它，React 将负责更新渲染的组件，以反映状态的变化。每次您更改状态时，React 都会找到最快的方法来更新您的渲染组件，并负责为您更新它。真漂亮。

为了创建某种状态，我们将使用一个钩子。钩子是 React 16.8 的新增功能，它们极大地简化了 React API，并允许以全新的方式构建和组合组件和功能。可以肯定的是，从现在开始，它们将成为我们创建组件的默认方式。

我们可以通过使用来自`React`的`useState`并传入 initialValue 作为第一个参数来创建一个状态。这个钩子将返回一个数组，其中第一个值是当前状态，第二个值是更新状态的函数。

```
const Counter = () => {
    const [count, setCount] = React.useState(0)
    const handleClick = () => setCount(count + 1)
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={handleClick}>Click Me!</button>
        </div>
    )
} 
```

这是我们第一个真正的动态组件🎉 🎉 🎉。这是 React 的真正优势所在。创建动态组件非常简单。

但是事情会变得更有趣。只有使用`useState`我们实际上不能做异步的事情，让我们现实一点，web 开发的主要部分是与 API 对话。那么，我们该怎么做呢？

# 效果

有时候你需要做些事情来应对变化。这就是我们所说的效果。您可以为组件生命周期中的各种事件触发效果，例如安装、卸载和更新。

为了演示这个概念，让我们做一些更真实的事情。让我们创建一个组件，从这个[公共 api](https://aws.random.cat/meow) 中获取一个随机的猫图像，并将其呈现在页面上。

对于效果，我们有另一个钩子`useEffect`。这个钩子接受两个参数:第一个是在组件生命周期的某个时刻执行的函数，第二个是可选数组，它列出了这个函数所依赖的内容。第二个论点可能会导致三种结果:

*   如果没有传递任何东西给它，React 会假设无论何时发生任何事情，都需要再次运行这个效果。
*   如果您传递一个空数组，React 将假定这个函数只需要运行一次，在组件的第一次渲染之后。
*   如果你把一些东西放在这个数组里面，React 会在每次数组里面的任何元素改变的时候运行这个效果。

我们开始吧:

```
const RandomCat = () => {
  const [ready, setReady] = React.useState(false)
  const [src, setSrc] = React.useState(null)
  const [error, setError] = React.useState(null)
  React.useEffect(() => {
    fetch("https://aws.random.cat/meow")
      .then(response => response.json())
      .then(data => {
        setReady(true)
        setSrc(data.file)
      })
      .catch(error => setError(error))
  }, [])
  if (!ready) return <p>Loading...</p>
  if (error) return <p>Oops, something went wrong!</p>
  return <img src={src} alt="random cat photo" />
} 
```

瞧，我们的组件向第三方 api 发出请求，获取图像地址并用图像标签呈现它。我们可以通过将它重构为一个**定制钩子**来做得更好。

# 自定义挂钩

我们不需要限制自己只使用 React 给定的钩子。我们可以创建自己的钩子，抽象出细节，只暴露重要的东西。让我们把手弄脏:

```
const useRandomCatImg = () => {
  const [ready, setReady] = React.useState(false)
  const [src, setSrc] = React.useState(null)
  const [error, setError] = React.useState(null)
  React.useEffect(() => {
    fetch("https://aws.random.cat/meow")
      .then(response => response.json())
      .then(data => {
        setReady(true)
        setSrc(data.file)
      })
      .catch(error => setError(error))
  }, [])
  return { ready, error, src }
}

const RandomCat = () => {
  const { ready, error, src } = useRandomCatImg()
  if (error) return <p>Oops, something went wrong!</p>
  if (!ready) return <p>Loading...</p>
  return <img src={src} alt="random cat photo" />
} 
```

由此带来的即时好处是，现在我们可以在任何我们认为合适的地方使用相同的`useRandomCatImg`。

这里应该注意几件事:

*   钩子**必须在函数组件**内部被调用。
*   惯例是钩子应该总是以`use`开头。这是因为 React 假设您遵循了这一点，并且只会在您遵循这一约定时警告您违反了这一约定。所以，一定要遵循它！

此外，请求过程中的整个管理状态确实有点冗长和笨拙，但请耐心等待，因为有了 React 悬念，这将很快变得更好。我现在不打算讨论这个问题，因为我们还没有到那一步，但是你应该知道这将会变得更好。

# 类组件

尽管钩子很棒，但它们只是最近才被正式引入，在此之前，所有这些反应能力都已经存在了，只是方式不同而已。尽管我们肯定会把钩子作为标准，但是类组件是存在的，它们不会消失。另外，你很可能会在 Hooks 出现之前的任何地方发现它们。

您通过创建一个类并扩展`React.Component`来创建一个类组件。通过这样做，您现在可以访问来自组件的生命周期方法，比如`componentDidMount`、`componentDidUpdate`、`componentShouldUpdate`、`componentWillUnmount`等等。您还可以实例化一个`state`并使用`this.setState`来改变它。

你可以用类组件做的一切，你都可以用钩子来做，反之亦然，除了`componentDidCatch`生命周期方法，但这也适用于钩子。

我们之前用钩子做的同一个`RandomCat`组件，用类看起来是这样的:

```
class RandomCat extends React.Component {
  constructor(props) {
    super(props)
    this.state = {}
    this.fetchRandomCatImg = this.fetchRandomCatImg.bind(this)
  }

  componentDidMount() {
    this.fetchRandomCatImg()
  }

  fetchRandomCatImg() {
    fetch("https://aws.random.cat/meow")
      .then(response => response.json())
      .then(data => {
        this.setState({
          error: null,
          ready: true,
          src: data.file
        })
      })
      .catch(error => this.setState({ error }))
  }

  render() {
    if (this.state.error) return <p>Oops, something went wrong!</p>
    if (!this.state.ready) return <p>Loading...</p>
    return <img src={this.state.src} alt="random cat photo" />
  }
} 
```

合理地更冗长，但它做同样的事情。重用这个逻辑也更加困难，因为由于使用了`this`,`fetchRandomCatImg`不能被共享以在其他地方使用，这使得它依赖于组件实例。这个问题有解决方案，主要是渲染道具和高阶组件模式，但这两者也增加了自己的问题。钩子只是在 React 应用程序上实现逻辑可重用性的一种更好的方式。

像往常一样，[丹·阿布拉莫夫](https://twitter.com/dan_abramov)有一篇[的精彩文章](https://dev.to/dan_abramov/making-sense-of-react-hooks-2eib)解释了钩子为什么这么好。

# 结论

状态和效果是 React 的基础。正是这 20%的人在 React 上做了 80%的事情。现在，您旅程的下一步可能应该是:

*   如果你没有经历过，去看医生。React docs 非常棒，目前正在被翻译成多种语言。如果翻译成你的语言还没有准备好，试着为之做出贡献。各版本文件的所有回复都可以在[这里](https://github.com/reactjs)找到。
*   学习如何使用路由器。我建议你去看看由[瑞安·弗洛伦斯](https://twitter.com/ryanflorence)制造的 [Reach 路由器](https://reach.tech/router)，因为它专注于可访问性。
*   学习如何处理[表单](https://reactjs.org/docs/forms.html)，专控组件。React 确实有一种不同的方式来处理表单输入，这在开始时可能会有点尴尬。帮自己一个忙，使用 [formik](https://github.com/jaredpalmer/formik) 。
*   了解如何测试 React 组件。Kent C. Dodds 在这个主题上有很多高质量的内容，他也是 [react-testing-library](https://github.com/kentcdodds/react-testing-library) 的作者，这是一个测试组件的神奇工具。我怎么强调测试的重要性都不为过。还有他写的这篇介绍图书馆的[文章](https://dev.to/kentcdodds/introducing-the-react-testing-library-2k5d)。看看这个。
*   查看一些状态管理解决方案。 [Redux](https://github.com/reduxjs/redux) 是最常用的，但一开始可能会让人不知所措，而且它不是唯一的一个。MobX 以简单著称。状态管理的一个很好的规则是只在你确实觉得应该使用它的时候使用它。很多时候，`Context`足以在树上遥远的组件之间共享状态。不过一开始你绝对不需要担心。
*   看看 React 生态系统上的 CSS 工具。我非常喜欢 CSS-in-JS 库，因为它在编写样式时给了你更多的能力，而且它非常适合 React，将你的样式转换成组件。我超级喜欢[风格组件](https://github.com/styled-components/styled-components)，但是[情感](https://github.com/emotion-js/emotion)也是一个不错的选择。[马克斯·斯托伊伯的](https://twitter.com/mxstbr)最近的文章[“我为什么用 JavaScript 写 CSS”](https://mxstbr.com/thoughts/css-in-js/)是一个很好的读物，如果你正在寻找一个更有说服力的支持 CSS-in-JS 的论点的话。

对于现在来说，这已经足够了，当然比我在这篇文章中打算的要多😄。不要因为我刚刚在这里抛出的链接数量而感到沮丧。慢慢来，用自己的时间尝试每个话题。更重要的是，接触人们，提出问题。React 社区非常有用，你可以在任何地方找到帮助( [reddit](//reddit.com/r/reactjs) ，twitter 上的开发者， [freeCodeCamp](https://www.freecodecamp.org/) 和许多其他地方)。

最后，如果你还在这里，请在下面的评论中告诉我你是如何学习 React 的。我很想听听。另外，点击下面按钮，当我的下一篇文章发表时，你会得到通知。

今天到此为止。✌️

*卡斯帕·卡米尔·鲁宾在 Unsplash 上拍摄的封面图片*