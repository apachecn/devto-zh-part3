# 重构一个小的 Next 应用程序来使用钩子

> 原文：<https://dev.to/selbekk/refactoring-a-small-next-app-to-use-hooks-2agj>

当 React 核心团队推出钩子的概念时，我在阅读文档的几分钟内就完全同意了。保持所有东西都是简单的函数，而不是处理类，绑定和生命周期方法对我来说太棒了。

> 如果你不熟悉 hooks，建议你去访问[官方文档](https://reactjs.org/hooks)。它们是很好的读物(尽管很长),会让你觉得你知道什么是钩子以及它们是如何使用的。

不过，就在胡克出生的同时，我的陪产假开始了。我很幸运得到了~6 个月的带薪假在家陪儿子！有很多乐趣，很多便便尿布和很多睡眠不足。完全没有钩子。

照顾我的儿子意味着我真的没有很多空闲时间来玩新的 API，我也没有任何“专业”的项目可以介绍给他们。然而，在过去的几天里，他睡得更好了——留给我几个小时打发时间。你好胡克斯！

就在两年多前，我买了一箱 3 升的酒和一个域名。圣诞节。我决定用 React 的文章创建一个降临节日历，并在几个晚上的时间里拼凑了一个应用程序。它基于 next . js——一个服务器端渲染 React 框架——并且非常简单。

换句话说——钩子的完美候选——重构。

本文将概述我重构整个应用程序的过程。这似乎是一个令人生畏的任务，但老实说并没有那么多工作。希望它能激励你做类似的事情！

## 为什么是 tho？

随着 React 核心团队不断迭代，你[不应该重构你现有的代码来使用钩子](https://reactjs.org/docs/hooks-intro.html#gradual-adoption-strategy)。他们提出这个建议的原因是因为没有真正的需要。类组件已经存在(至少在可预见的将来)，使用钩子只能获得很少的性能。换句话说，这将是一个没有任何明确值的重构。嗯，至少表面上是这样。

我对于重构旧的基于类的组件来使用这些新的钩子的观点很简单:*这是一个很好的实践！由于我现在没有时间做任何真正的项目，这个小小的重构正是我所需要的，来巩固我所读过的东西。如果你在工作中有空闲时间，我建议你也考虑这样做。*

### 为什么不是 tho？

注意你不能在类组件中使用钩子。如果你将基于 hoc 和渲染道具的组件重构为定制钩子，你将不能在类组件中使用它们。有一些方法可以解决这个问题，但是现在，要小心一点。或者重构你所有的代码😁

## 代码！

首先，我们来介绍一下代码:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [塞尔贝克](https://github.com/selbekk) / [反应——圣诞节](https://github.com/selbekk/react-christmas)

### 融入构图的精神

<article class="markdown-body entry-content container-lg" itemprop="text">

# 反应.圣诞节

## 发展

要运行开发服务器，运行`yarn dev`。

## 部署

使用`yarn deploy`部署。

## 创造你自己的！

分叉这个项目，把`./config.js`里的东西改了就可以开始了。如果您在那之后发现任何更多的 React-specific，请提交一个 pull 请求，将这些文本等移动到`./config.js`。

## 写内容

所有内容都在`./content/`文件夹中，按年份分类。如果你想添加 2018 年的文章，创建一个名为`./content/2018`的文件夹，并开始创建 Markdown 文件。

降价文件应该被命名为`01.md`、`02.md`等，一直到`24.md`。每篇文章都应该以 Frontmatter 格式的一些元数据开始，就像这样:

```
title: Get started with create-react-app
lead: Creating your first React app usually starts off with a 30 minute crash course with Webpack, Babel and a whole lot
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/selbekk/react-christmas)

该应用程序实际上非常简单。它有一个 Markdown 格式内容的文件夹，通过 API 向 Next.js 应用程序公开。后端是一个简单的 Express 服务器，前端也非常简单。

事实上，代码非常简单，没有太多需要重构的类组件！虽然有一些，但我将一一介绍。

### [T3 记得升级`react`和`react-dom`](#remember-to-upgrade-raw-react-endraw-and-raw-reactdom-endraw-)

为了使用钩子，我们需要使用一个支持它们的 React 版本。经过大量的 Twitter 炒作，终于在 16.8.0 发布了。所以我做的第一件事就是更新我的反应部门:

```
- "react": "^16.4.1",
- "react-dom": "^16.4.1", + "react": "^16.8.3",
+ "react-dom": "^16.8.3", 
```

Enter fullscreen mode Exit fullscreen mode

(是的，我知道版本范围允许我在这里运行一个`npm update`,但是我喜欢明确版本需求)

## 重构 BackgroundImage 组件

我重写的第一个组件是一个`BackgroundImage`组件。它执行了以下操作:

*   当它安装后，检查屏幕尺寸。
*   如果屏幕尺寸小于 1500 px，请要求图像的适当缩放版本。
*   如果屏幕尺寸为 1500 像素或更宽，则什么也不做

代码看起来像这样:

```
class BackgroundImage extends React.Component {
  state = { width: 1500 }
  componentDidMount() {
    this.setState({ width: Math.min(window.innerWidth, 1500) });
  }
  render() {
    const src = `${this.props.src}?width=${this.state.width}`;
    return (
      <Image src={src} />
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

将这个组件重写为一个定制的钩子并不困难。它保留一些状态，在 mount 上设置该状态，并呈现依赖于该状态的图像。

我的第一个重写方法是这样的:

```
function BackgroundImage(props) {
  const [width, setWidth] = useState(1500);
  useEffect(() => setWidth(Math.min(window.innerWidth, 1500)), []);
  const src = `${props.src}?width=${width}`;
  return <Image src={src} />; } 
```

Enter fullscreen mode Exit fullscreen mode

我使用`useState`钩子来记住我的宽度，我将它默认为 1500 px，然后我使用`useEffect`钩子将它设置为窗口安装后的大小。

当我查看这段代码时，出现了一些我之前没有想到的问题。

*   我不会总是先下载最大的图片吗，这样？
*   如果窗口大小改变了怎么办？

让我们先处理第一个问题。由于`useEffect`在 React 将其更改刷新到 DOM 后运行，第一次渲染将总是请求 1500 px 版本。这并不酷-如果它不需要一个巨大的图像，我想为用户节省一些字节！所以我们来稍微优化一下:

```
function BackgroundImage(props) {
  const [currentWidth, setCurrentWidth] = useState(
    Math.min(window.innerWidth, maxWidth)
  );
  const src = `${props.src}?width=${currentWidth}`;
  return <Image src={src} />; } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，如果窗口大小由于 resize 事件而改变，我们想要下载一个新的图像:

```
function BackgroundImage(props) {
  const [currentWidth, setCurrentWidth] = useState(
    Math.min(window.innerWidth, 1500)
  );
  useEffect(() => {
    const handleResize = () => setCurrentWidth(
      Math.min(window.innerWidth, 1500)
    );
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);
  const src = `${props.src}?width=${currentWidth}`;
  return <Image src={src} />; } 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但是我们在调整大小时会请求大量的图片。让我们去抖这个事件处理程序，所以我们每秒最多只请求一个新图像:

```
import debounce from 'debounce'; // or write your own

function BackgroundImage(props) {
  const [currentWidth, setCurrentWidth] = useState(
    Math.min(window.innerWidth, 1500)
  );
  useEffect(() => {
    // Only call this handleResize function once every second
    const handleResize = debounce(() => setCurrentWidth(
      Math.min(window.innerWidth, 1500)
    ), 1000);
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);
  const src = `${props.src}?width=${currentWidth}`;
  return <Image src={src} />; } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们在做饭！但是现在我们的组件中有大量的逻辑，所以让我们把它重构到自己的钩子中:

```
function useBoundedWidth(maxWidth) {
  const [currentWidth, setCurrentWidth] = useState(
    Math.min(window.innerWidth, maxWidth)
  );
  useEffect(() => {
    const handleResize = debounce(() => {
      const newWidth = Math.min(window.innerWidth, maxWidth);
      if (currentWidth > newWidth) {
        return; // never go smaller
      }
      setCurrentWidth(newWidth);
    }, 1000);
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, [maxWidth]);

  return currentWidth;
}

function BackgroundImage(props) {
  const currentWidth = useBoundedWidth(1500);
  const src = `${props.src}?width=${currentWidth}`;
  return <Image src={src} />; } 
```

Enter fullscreen mode Exit fullscreen mode

看那个！可重复使用，易于测试，我们的组件看起来很神奇，我想我在某个时候看到了彩虹。漂亮！

请注意，我还借此机会确保我们下载的图像不会比我们开始时下载的图像小。那只会是一种浪费。

## 页面跟踪钩

好吧！到下一个组件。我想重构的下一个组件是页面跟踪组件。基本上，对于每个导航事件，我都会将一个事件推送到我的分析服务中。最初的实现是这样的:

```
class PageTracking extends React.Component {    
  componentDidMount() { 
    ReactGA.initialize(
      this.props.trackingId, 
    );  
    ReactGA.pageview(this.props.path);  
  } 
  componentDidUpdate(prevProps) {   
    if (prevProps.path !== this.props.path) {   
      ReactGA.pageview(this.props.path);    
    }   
  } 
  render() {    
    return this.props.children; 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，这是我包装应用程序的一个组件。如果我愿意，它也可以作为一个特设项目来实现。

由于我现在是钩子专家，我立即意识到这看起来像是定制钩子的主要候选。所以让我们开始重构吧！

我们在装载时初始化分析服务，并在装载时和路径改变时注册页面视图。

```
function usePageTracking({ trackingId, path }) {
  useEffect(() => {
    ReactGA.initialize(trackingId);
  }, [trackingId]);

  useEffect(() => {
    ReactGA.pageview(path)
  }, [path]);
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们调用`useEffect`两次——一次用于初始化，一次用于跟踪页面视图。初始化效果只在`trackingId`变化时调用，页面跟踪一只在`path`变化时调用。

要使用它，我们不必在渲染树中引入一个“人造”组件，我们可以在顶层组件中调用它:

```
function App(props) {
  usePageTracking({ trackingId: 'abc123', path: props.path });
  return (
    <>
      <SiteHeader />
      <SiteContent />
      <SiteFooter />
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这些自定义挂钩如此清晰。你指定你想要发生什么，你指定你想要这些效果什么时候重新运行。

## 总结

重构现有代码以使用钩子是有益的，也是一次很好的学习经历。无论如何，你都不需要这么做，而且*有*一些用例你可能想推迟迁移——但是如果你看到了重构一些代码的机会，*就这么做吧*！

我希望您已经从我如何应对这一挑战中学到了一些东西，并受到启发在您自己的代码库中做同样的事情。黑客快乐！