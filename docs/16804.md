# 声明式风格定义第 1 部分

> 原文：<https://dev.to/busypeoples/declarative-style-definitions-pt1-1l97>

***注意:这是更好地理解主题的第一次尝试。不是造型或设计方面的专家。
以下文章收集了长期以来一直在思考这个话题的人们的想法:
[亚当·摩斯](https://twitter.com/mrmrs_)[陈宸](https://twitter.com/chantastic)[乔恩·戈尔德](https://twitter.com/jongold)[苏尼尔·帕伊](https://twitter.com/threepointone)等等。
***

当构建 ui 时，我们通常会很好地把握最初的需求。我们围绕这些需求选择工具和概念，并实现类似初始定义的 UI。但是随着时间的推移，这些需求开始演变。更多的特性或对初始定义的改变需要改变应用程序的部分，有时甚至不仅仅是小的重构和重写。大多数用户界面工作倾向于重复现有的解决方案，但是有着与现有的不一致的明确需求。通常意味着从头开始，每次我们开始构建一个新的应用程序。
这些问题可能来自我们处理 UI 工作的方式，主要是命令式风格，而声明式风格可能更适合我们专注于构建的应用程序。

“用我们目前的工具，我们告诉计算机如何设计我们头脑中的视觉(通过点击屏幕上每个元素的输入设备)；在我们未来的工具中，我们将告诉我们的计算机我们想要看到什么，并让它们知道如何移动元素来达到目的。”
˘

[声明式设计工具，乔恩·戈尔德](https://jon.gold/2016/06/declarative-design-tools/)

声明式方法很好地满足了我们针对变化进行优化的需求。一个有趣的方面是，我们不想写更多的样式定义，我们想要的是能够以任何人都可以使用的方式组合现有的定义。添加更多的样式应该是例外。

“当我读到或听到关于如何扩展一个应用程序的 css 的想法时，大多数谈论的都是如何编写 css。缩放 css 的真正方法，是停止编写 css。”

[CSS 和可伸缩性，亚当·莫尔斯](http://mrmrs.cc/writing/2016/03/24/scalable-css/)

那么，我们有什么工具和想法可以让*为改变*而优化成为一项可行的任务呢？

为了更好地理解，让我们看一看由 Adam Morse 编写的功能性 css 库 Tachyons。通过阅读类名，我们可以理解样式应该做什么。通过更改类名，我们可以确保不会破坏应用程序中其他地方的另一个组件。

这种方法让我们很好地理解了为什么组合是处理样式复杂性的更好方式。

使用 React 和它的“组件思维”方法，我们获得了另一个强大的概念。如果一切都是组件呢？甚至一个页面也只是一个组件树。React 带有内置于系统中的组合。这是在 React 中构建应用程序的唯一方法。当使用模板方法时，这听起来可能违反直觉，但是我们得到的是将组件从 DOM 和其他副作用中解放出来。我们声明我们想要渲染的内容，React 负责底层的底层操作。我们不需要手动处理 DOM 操作。我们甚至可能一开始就没有渲染到 DOM。

通过能够构建独立的组件，不依赖于任何上下文，我们可以移动这些组件，就像小积木一样。

这就是我们已经建立的 ui。

这些范例帮助我们针对变化进行优化。

先说一个缺失的方面。能够以声明的方式声明我们的样式。

我们已经讨论过超光速粒子，它在类的层次上运行，但是如果它能隐藏事实并在组件层次上实现样式呢？

## 陈述性方法

让我们考虑一下声明式样式方法可能是什么样子。以下面的例子为例，我们定义了一些样式构造，然后将它们通过管道传输到指定的输出。这个输出可以是 DOM 元素，也可以是 React 或 Preact 元素。

```
const style = (key, value) => style => `${style}${key}:${value};`;
const applyStyle = element => style => {
  element.setAttribute("style", style);
};

pipe(
  style("color", "red"),
  style("width", "4rem"),
  style("height", "2rem"),
  applyStyle(document.getElementById("main"))
); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用类名或其他构造做同样的事情。我们主要感兴趣的是能够根据需要构建我们的风格。在超光速粒子中，我们已经有了类名和风格之间的一对一映射。这使我们能够在元素级别组合这些类名。这很有用。

当将样式定义从特定格式中分离出来时，我们可以利用更多的东西。例如，我们可以利用类型和现有的语言结构。

如果你正在编写 React，你可以使用比 CSS 类名更强大的样式构造。你有组件。”

[“音阶”FUD 和风格成分陈宸](https://medium.learnreact.com/scale-fud-and-style-components-c0ce87ec9772)

## 举例

是时候看看我们能够从组件级定义样式中获得什么了？

“将样式抽象成组件让我们可以在一个地方改变应用程序的实现。”
[【音阶】FUD 和风格成分，陈宸](https://medium.learnreact.com/scale-fud-and-style-components-c0ce87ec9772)

让我们看一个例子。我们需要显示一个包含名称，标题，简短描述以及图像的用户配置文件。我们的第一种方法是定义一个`Profile`组件，它可以显示给定用户所需的信息。

```
const Profile = ({user}) => (
  <article className="mw5 center bg-white br3 pa3 pa4-ns mv3 ba b--black-10">
    <div className="tc">
      <img src={user.image} className="br-100 h4 w4 dib ba b--black-05 pa2" title={`${user.firstName}  ${user.lastName}`}>
      <h1 className="f3 mb2">`${user.firstName}  ${user.lastName}`</h1>
      <h2 className="f5 fw4 gray mt0">{user.role}</h2>
      <hr className="mw3 bb bw1 b--black-10">
    </div>
    <p className="lh-copy measure center f6 black-70">
      {user.description}
    </p>
  </article> ) 
```

Enter fullscreen mode Exit fullscreen mode

上述组件是基于超光速粒子卡的例子。开发者现在可以使用已定义的`Profile`组件，并根据需要将其集成到应用程序的任何部分。

```
<Profile user={user} />; 
```

Enter fullscreen mode Exit fullscreen mode

样式定义被封装在组件内部，从而排除了修改外观和感觉的可能性。只要看看我们的组件，我们就可以看到，改变任何样式定义都是非常复杂的，因为类名到处都在使用。我们已经可以发现一些重构的机会。同样，我们的`Profile`可以分成更多的组件。让我们从 article 开始，它可能是一个`Card`组件。

```
type CardProps = {
  bg: string;
  borderColor: string;
  maxWidth: number;
  children: Array<JSX.Element>;
};

const Card = ({
  bg = "white",
  borderColor = "black",
  maxWidth = 5,
  children
}: CardProps) => (
  <article
    className={`mw${maxWidth} center bg-${bg} br3 pa3 pa4-ns mv3 ba b--${borderColor}-10`}
  >
    {children}
  </article> ); 
```

Enter fullscreen mode Exit fullscreen mode

我们不仅提取了`Card`组件，还定义了以可控方式改变外观和感觉的可能性。我们可以改变背景或边框颜色或最大宽度。作为组件开发人员，我们可以确保什么可以改变以及如何改变。我们的`Card`组件已经独立于`Profile`组件。在构建产品卡时，我们可以自由使用`Card`组件，并根据需要调整外观。

虽然我们可以进一步分解组件，但我们也可以通过添加类型来改善整体体验。让我们回到`Card`组件。通过添加类型定义，我们可以确保只传入可能的值。

```
type MaxWidth = 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | "-100" | "none"; 
```

Enter fullscreen mode Exit fullscreen mode

尽管这看起来可能是一项无聊的任务，但我们可以改善开发人员的体验，并确保只传入正确的值。在使用 React 时，我们也可以使用 *propTypes* ，但是这种方法在特定的框架或库之外更适用。

能够提供类型定义无疑是我们从组件中获得的另一个副作用。

但是，我们可以做更多的事情来改善整体体验，并从一开始就防止不正确的样式定义。我们可以输入所有可能的样式，从而使开发人员能够利用自动完成和快速反馈循环，例如，当传入不正确的值时会收到错误消息。

```
enum BackgroundColor {
  White,
  Gray,
  // add all possible colors
} 
```

Enter fullscreen mode Exit fullscreen mode

这使我们能够对我们的造型进行约束。

```
type CardProps = {
  bg: BackgroundColor;
  borderColor: BorderColor;
  maxWidth: MaxWidth;
  children: Array<JSX.Element>;
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以确保只传入可能的类型，我们还可以控制哪些类型可以被覆盖，同时还可以提供默认设置来使用现成的组件。

我们通常需要组件的子集，并且可以通过组合这些子集来组成更多的组件。
例如`Rebass`提供 8 个组件，可用作构建模块，通过组合这些模块来构建更多组件。这 8 个组件是:`Box`、`Flex`、`Text`、`Heading`、`Link`、`Button`、`Image`和`Card`。`Rebass`使用`styled-system`定义样式，遵循基于约束的方法。

一旦我们有了底层构件，我们就可以开始使用它们来构建更具体的组件。我们也许可以通过组合这些低级的构件来构建我们的`Profile`。

在第 2 部分中，我们将更具体地介绍这些想法，并构建一个完全可行的示例，更详细地了解我们通过将底层机制作为实现细节所获得的好处。

***如果您有任何问题或反馈，请在此留言或通过 Twitter 联系:***