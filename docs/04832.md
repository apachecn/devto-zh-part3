# React 导航与 React 原生导航:哪个适合你？

> 原文：<https://dev.to/bnevilleoneill/react-navigation-vs-react-native-navigation-which-is-right-for-you-1bfo>

[![](img/b70b12d2fb5202f15c487566c7b8d68a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fIvTNsdl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AnYZqL7G4A6FG7Q3z2qj9_Q.jpeg)

> 免责声明:这篇文章包含了我的个人观点，它是建立在使用 React 本地应用的多个导航库的经验之上的。

当使用 React Native 开发移动应用程序时，最重要的步骤之一是为您的项目选择完美的导航库。导航是应用的支柱，对用户体验有很大的影响。

没有任何单一的解决方案可以满足所有需求。有[个导航库](https://github.com/search?p=1&q=react+native+navigation&type=Repositories)可供我们选择。

React Navigation 是使用最广泛、开发最活跃的库之一。也是 React 原生团队推荐的[方案之一。这是脸书推动最多的社区解决方案。](https://facebook.github.io/react-native/docs/navigation)

在这里，我们将比较 React Navigation 和其著名的替代产品 React Native Navigation (RNN ),权衡为您的应用程序选择导航库的关键要素。

我们将分析 React 导航蓬勃发展的场景，以及一些不发达的场景。在后一种情况下，我们将尝试分析为了实现你想要的特性，你需要有多复杂，或者 RNN 在这些情况下会有多好。

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 表现

为应用选择导航库时，性能是首要参数。每秒显示的帧数决定了界面的流畅度。大多数高性能应用始终保持 60fps，尤其是在交互和过渡期间。

这个基准留给我们大约 16.67 毫秒的时间来完成生成下一帧所需的所有工作。如果我们错过了这个窗口，我们将会丢失一帧，这会使我们的用户界面看起来没有反应和笨拙。

在我们的 React 原生应用中，大多数业务逻辑驻留在 JS 线程上，包括 API 调用、状态管理和触摸事件的处理。React 导航与应用程序的其余部分完全在相同的 JS 线程上工作。

当您推送一个新的路由时，JavaScript 线程需要在屏幕上呈现组件，以便将正确的命令发送到本机端来创建后备视图。这可能会导致具有大量屏幕和复杂界面的应用程序出现性能瓶颈。

这个问题的一个解决方案是将整个屏幕(或屏幕的几个部分)的根组件附加到本机视图层次结构。通过使用 react-native-screens 和 react 导航，这个解决方案现在变得很容易实现。[下面是这样做的指南](https://reactnavigation.org/docs/en/react-native-screens.html)。

在最近的版本中，所有的动画都是使用内置导航器中的原生驱动程序来完成的。除此之外，React 导航团队正在使用 react-native-reanimated 来提高手势释放的性能。为了处理手势，react-native-gesture-handler 在最近的版本中用于将手势从 JS 线程卸载到主线程。

与 React 导航相比，默认情况下，RNN 会将你的所有屏幕都视为单独的 React 应用程序，这使得你的应用程序更具性能。然而，这也带来了一些复杂性——尤其是在与像 Redux 和 react-intl 这样的库集成时，这些库要求您包装您的应用程序。我们将在稍后的集成比较中讨论这一点。

### 使用多台路由器

在需要登录的应用程序中，开发人员通常更喜欢不同的导航器来分别处理非登录和登录旅程。使用 React Navigation，这很容易实现，因为它是一个基于 JS 的导航器，并且提供了令人敬畏的 API。当使用多个路由器时，请查看 React Navigation 的本[指南，以避免任何麻烦。](https://reactnavigation.org/docs/en/common-mistakes.html#explicitly-rendering-more-than-one-navigator)

相反，RNN 允许你在应用程序的任何地方设置导航的根，模仿本地 API，你可以开始一个新的活动或片段，并清除现有的堆栈。

### 与其他第三方库集成

作为一个基于 JS 的导航器，React Navigation 可以与任何第三方库顺利集成，而 RNN 可能会受到与原生平台紧密耦合或需要包装在整个应用程序周围的库的影响。

例如，RNN 为集成 React Native 脸书 SDK 等包提供了单独的指南，并提供了一个现成的 API 来将 Redux 集成到您的应用程序中。

### 与现有应用程序集成

React Native 的主要优势之一是它能够与您现有的任何本机应用程序集成。在这种情况下，与现有的本地解决方案相比，任何基于 JS 的导航器都可以工作得很好。

这使得 [React Navigation 比 RNN](https://github.com/wix/react-native-navigation/issues/1234#issuecomment-431666444) 更有优势，尤其是如果你希望用 React Native 为你现有应用的几个模块提供动力，就像脸书和许多其他棕色地带应用一样。

### 开机努力

如果你和你团队中的其他开发者没有本地应用开发的背景，你可能想要一个没有陡峭集成指南的解决方案。在集成过程中，React 导航与 React 原生导航相比有一点优势。

虽然有些开发人员有时会觉得入门有点困难，但 Wix 的官方指南可以帮助你开始工作。Wix 还有一个非常有用的[不和谐聊天](https://discordapp.com/channels/242515947020222464/339371162855276544)，可能是你遇到任何麻烦时的首选。

### 深度链接

在这个客户充当营销代理的社交媒体时代，大多数应用程序都有一种简单的方式来允许用户在他们的社交渠道上分享信息和活动。这要求我们在应用程序中启用深度链接。

假设您的应用程序显示博客，您希望用户在进入您的应用程序时直接登陆帖子屏幕。React Navigation 和 RNN 目前都没有提供一种简单的方法来做到这一点。这两种方法在静态定义路线的情况下效果最佳。尽管您可以实现一些变通方法来实现这个特性，但是这也是以增加代码库的复杂性为代价的。

### 开发团队和后盾

当选择任何库时，对你的应用有很大影响的主要因素之一是维护该库的开发者和贡献者的数量。

React Navigation 背后的团队由 React 本地核心和 expo 的积极贡献者组成。除此之外，他们正在开发许多 React 原生应用程序，这些应用程序可以让他们从更广阔的视角来看待未来可能出现的问题。

今天，大量使用 React Native 构建的应用程序，包括 CNN、彭博和城市词典，都在使用 react-navigation。这里有一个 [Twitter 线程](https://twitter.com/notbrent/status/1116717557107417090)列出了更多这样的应用。

RNN 得到了维克斯的支持。他们在生产中将其用于自己的应用程序，并真正关注它的持续进步和发展。

### 总而言之

选择导航库是应用开发的关键一步。任何未经深思熟虑的决定都可能会损害应用程序的可伸缩性，或者在应用程序变大时产生其他问题。

依我看，React 导航是目前最好的导航解决方案之一。它是一个被广泛采用的库，非常适合大多数用例。尽管存在某些已知的限制，但这是一个首选的解决方案。请记住，由于像 react-native-screens 和 react-native-gesture-handler 这样的库，它现在的性能得到了提升。

为了更深入的分析，你可以观看 Brent Vatne 的精彩演讲，他是 React Navigation 的主要贡献者。Brent 非常全面地比较了这两种导航解决方案。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

React 导航与 React 原生导航:哪个更适合你？最早出现在 [LogRocket 博客](https://blog.logrocket.com)上。