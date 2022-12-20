# 使用 React 转换组改进您的用户界面

> 原文：<https://dev.to/bnevilleoneill/improve-your-ui-with-react-transition-group-3jmi>

[![](img/51bf3fff071d66274668ebdf1a41b179.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XwGlLrJj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AMLFqXC9V1urGVH0vNT1Kkw.jpeg)

创建交互式 ui 最常被忽视的原则之一是过渡。幸运的是，在过去的几年里， [React.js](https://reactjs.org/) 和其他专注于组件的框架已经改变了我们对 ui 以及如何构建 ui 的看法。

[React 转换组](https://github.com/reactjs/react-transition-group)允许我们以声明性和高效的方式将这些组件转换进和转换出 DOM。在本文中，我们将使用简单的示例集中讨论 React 转换组的 CSSTransition 和 TransitionGroup 组件。

### 先决条件

在我们进一步讨论之前，本文假设:

*   您的计算机上安装了 Node.js ≥v6
*   您的机器上安装了 npm
*   你对 React.js 有了基本的了解

### 入门

要安装，请在终端中运行以下命令:

[https://medium . com/media/738 e 482 f 32 c 270 F4 ca 77 b 08d 99 e 073 BC/href](https://medium.com/media/738e482f32c270f4ca77b08d99e073bc/href)

### 使用 CSS 转换一个 React 组件

CSSTransition 组件允许我们将转换应用于进入和离开 DOM 的元素。我们可以通过使用以下道具来实现这一点:

*   in:用于控制元素外观的布尔值
*   超时:表示进入或离开 DOM 所需的毫秒数
*   unmountOnExit:表示当元素消失时，它实际上将完全离开 DOM。下面是它的代码片段:[https://medium . com/media/f 6448 a2 c 30997 DBE 9896 ee 9 D2 db 99 f 69/href](https://medium.com/media/f6448a2c30997dbe9896ee9d2db99f69/href)

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 简单列表示例

下面是我们关于如何使用 React Transition Group 的 CSSTransition 的第一个示例:

#### CSS 转换前

[CSSTransition 组件(转换前)- CodeSandbox](https://codesandbox.io/embed/5k6qo23kpn)

#### **后** CSSTransition

[CSSTransition 组件(带过渡)- CodeSandbox](https://codesandbox.io/embed/wy4rnqly2k)

#### 如何使用 CSSTransition 创建过渡

在第一个代码示例中，我们有一个没有过渡的普通组件。只要单击 list 按钮，就会呈现一个有序列表，没有延迟，也没有额外的 CSS 样式。

但是当我们决定让这个例子更生动一点时，我们在使用标记的同时安装 react-transition-group，并将 this.state.showList 中的信息作为道具传递给 in，这使我们能够使用 CSS 添加一些过渡。

超时属性允许我们在列表离开 DOM 时应用一个转换。然后我们转向 style.css，为过渡添加一些样式。CSSTransition 为元素的进入和离开提供了四个关键的类名:enter、enter-active、exit 和 exit-active。

[https://medium . com/media/0376 b6f 46981d 02 CBF 2d a 6221 EC 28 b 9b/href](https://medium.com/media/0376b6f46981d02cbf2da6221ec28b9b/href)

然后，在这些 CSS 类中，我们可以在类中添加一些很棒(但很简单)的 CSS，使子组件看起来像是从按钮中生长出来的。

[https://medium . com/media/F3 b 512 ce 07 bfede 85 e 413 b 6787 CFC 163/href](https://medium.com/media/f3b512ce07bfede85e413b6787cfc163/href)

请注意，在上面的代码演示中，您会注意到 list-transition-enter 和 list-transition-exit-active 具有相同的值，因为它们是组件的开始和结束状态。然而，只有当类名处于活动状态时，才会发生转换。

### 添加出现道具以显示载入过渡

列表的初始状态被设置为 false。但是如果我们希望它在页面装载到 DOM 时显示呢？我们可以通过将 showList 的状态更改为 true 来实现这一点，但是使用如下所示的“出现”属性，过渡不会显示:

[https://medium . com/media/734 a 089821 efb 9 f 65 c 21 c 93 e 16649 CDA/href](https://medium.com/media/734a089821efb9f65c21c93e16649cda/href)

在 CSS 文件中，为。列表-过渡-出现与。列表-过渡-输入和。list-transition-exit-active，因为它发生在组件被安装时，并且它的唯一功能是允许转换像它出现时那样显示。

[https://medium . com/media/b 81 D6 fc 6a 2 a5 ad 5d 59 f 6179 c17b 1231 e/href](https://medium.com/media/b81d6fc6a2a5ad5d59f6179c17b1231e/href)

### 使用进入和退出道具

有时，如果应用程序要求在组件的转换生命周期的某个部分禁用转换，我们可以在组件中这样做，而无需编辑 CSS 或禁用类名。我们使用进入和退出道具来实现这一点，如下所示:

[https://medium . com/media/0ae 7496 c8 ceca 24899236 bb 0f 8d 27916/href](https://medium.com/media/0ae7496c8ceca24899236bb0f8d27916/href)

这停止了。列表-过渡-活动和。列表-过渡-输入正在工作的类。

[https://medium . com/media/04d c 9818 efdc 213 ccfd 539254 d4d 0907/href](https://medium.com/media/04dc9818efdc213ccfd539254d4d0907/href)

### CSS transition 组中的更多生命周期道具

我们可以使用生命周期道具来瞄准产品化阶段的特定时间。这些生命周期正如其名称所暗示的那样:

*   onEnter:当单击按钮并执行操作时激发
*   onEntering:当信息进入 DOM 时触发
*   onEntered:表示信息已经进入 DOM
*   onExit:本质上是在启动元素退出操作时触发
*   onExiting:当信息退出 DOM 时触发
*   onExited:表示信息已经离开 DOM

假设我们需要突出我喜欢做的最重要的活动。我们可以在列表呈现后突出显示颜色，并在突出显示之前添加一个延迟过渡。然后，我们的 CSSTransition 组件变成:

[https://medium . com/media/ff bde 4 f 81840 aa 01 e 070292 B3 FB 16 DD 1/href](https://medium.com/media/ffbde4f81840aa01e070292b3fb16dd1/href)

this.listSwitch 状态用于为我们想要突出显示的爱好设置一个条件类。所以当 highlightedHobby 为真时，我们将得到这个列表项的活动变体:

[https://medium . com/media/77 db 17 C2 e 882 D7 bcc 9149 e 9689d 34445/href](https://medium.com/media/77db17c2e882d7bcc9149e9689d34445/href)

条件类名看起来像这样:

[https://medium . com/media/68e 31108472 BDB 9 F6 ef 1b 3157 ed 0463 a/href](https://medium.com/media/68e31108472bdb9f6ef1b3157ed0463a/href)

当它打开时，我们看到编写 JavaScript 在 500 毫秒的延迟后变成蓝色，这比列表项的转换晚 100 毫秒，它在退出时返回。因为这发生得如此之快，我们看不到它离开；但是如果您使用开发人员工具检查该元素，您会注意到它。

### 使用 TransitionGroup 和 CSSTransition 将过渡应用于列表中的元素

使用这个代码示例，我将解释它在创建有趣的过渡中的用途。

[列表-示例-代码沙盒](https://codesandbox.io/embed/km4jyy4n0v)

从代码示例中，我们可以看到 TransitionGroup 映射到 favorite music 数组，并使用 CSSTransition 组件返回每个数组。

[https://medium . com/media/14 CBA 0 ed 9 db 48 b5 a 62 ca 5662 efdd 4d 57/href](https://medium.com/media/14cba0ed9db48b5a62ca5662efdd4d57/href)

从上面的代码示例中，我们可以看到 TransitionGroup 组件呈现了一个组件，我们可以设置这个来呈现任何东西。可能是 UL_，_ div_，_ p_，_ option_，_ 等。但是当我们不想呈现任何组件时，我们可以将其设置为{null}:

### 使用 JSS 转场

我们一直使用普通的 CSS 来实现我们的 CSS 转换；现在，我们将重构我们的 CSS 成为 JavaScript 对象。我们将从创建一个 styles.js 文件开始，并在对象中转换我们的样式，如下所示:

[https://medium . com/media/94935 EB 28 ed 7 ab 31d 95 a 69814 f 4237 a 9/href](https://medium.com/media/94935eb28ed7ab31d95a69814f4237a9/href)

然后，上面的代码片段被导入到我们的 refactor.js 中，如下所示:

从'导入样式。/styles '；

样式的实现是使用从 react-jss 导入的 InjectSheet 完成的:

从“react-jss”导入 injectSheet

这为我们提供了类道具，我们可以用它来访问 style.js 中的样式，如下所示:

[https://medium . com/media/23e 91 e 54385 b 986690 a 9354905 fccfcc/href](https://medium.com/media/23e91e54385b986690a9354905fccfcc/href)

注意，我们在这里使用类名，而不是类名，所以我们可以提供多个类名。

这些转换是通过传递一个带有 enter_、_ enter-active_、_ exit_、_ 和 enter-active 键的对象来添加的，它们将引用 JSS 类名。

[https://medium . com/media/d 35206 e 845 e 3d 69 e 6383 DD 436 BF 66 DC 2/href](https://medium.com/media/d35206e845e3d69e6383dd436bf66dc2/href)

[CSSTransition 组件(使用 JSS) - CodeSandbox](https://codesandbox.io/embed/ry76073x6n)

### 结论

平滑的过渡使你的应用程序的用户体验更加动态，更受终端用户的欢迎。React Transition Group 帮助我们用更少、更容易理解的代码实现了这一点。编码快乐！

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *