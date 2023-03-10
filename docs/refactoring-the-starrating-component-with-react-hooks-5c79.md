# 用 react 钩子重构星级组件

> 原文：<https://dev.to/eveporcello/refactoring-the-starrating-component-with-react-hooks-5c79>

2017 年春天，我们发布了我们的第一本[奥莱利的书](http://shop.oreilly.com/product/0636920049579.do)、*学习反应*。在我听说他们要把这本书的最终版本送到印刷厂的那天，我在 Twitter 上提出了一个简单的请求:

> 这个星期就要送到印刷厂了！React 和相关的库维护者:请在 5 年左右的时间里不要改变任何东西。成交？😀😎[https://t.co/njHdsKv6bf](https://t.co/njHdsKv6bf)
> 
> — Eve Porcello ([@eveporcello](https://dev.to/eveporcello) ) [April 24, 2017](https://twitter.com/eveporcello/status/856646889772466176?ref_src=twsrc%5Etfw)

我觉得这很合理。5 年内不要改变任何事情。两年后，很多事情都变了，公平地说，有人警告我:

> 但是如果我们不这样做，你就没有借口写第二版了！🙃
> 
> — Sophie Alpert (@sophiebits) [April 27, 2017](https://twitter.com/sophiebits/status/857470144338984960?ref_src=twsrc%5Etfw)

很明显，没有人听我的推特，但有很好的理由。随着 React 的发展，它会继续变得更好。我们可以通过在现有的应用程序中加入新的模式来利用新的优势。在这本书的第一版中，我们开发了一个叫做颜色管理器的应用。这是一个应用程序，允许人们添加、编辑和删除他们最喜欢的颜色列表。该应用程序的一个关键功能是评级，人们可以为每种颜色选择一个星级。

让我们看看第一版中的颜色管理器，然后我们将对它进行升级，以使用今年晚些时候将出版的第二版的最新语法。(是的，这是一个漫不经心的声明，我们正在开发第二版的 *Learning React* ，它将包含所有内容的最新更新。会很棒的。)

颜色管理器的用户界面如下所示:

[![color-organizer](img/50559bbf92f277181012a6dd930b51b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SR1uJu2D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://moonhighway.com/static/9f9a1b348694536fbd25402239d8939c/4a731/color-organizer.png)

这里有几个组件在起作用，但是让我们把重点放在`<StarRating />`组件上:

[![star rating](img/25b4e255fc5161a4d3f854d638ef5395.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lX7RyP25--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://moonhighway.com/static/690cdc630437c71e78fbb872edb095ce/26524/star-rating.png)

评级由两个部分组成:T0 和 T1。我们需要一种方法来跟踪明星是否被选中以确定评级。我们还需要允许用户点击一颗星来应用特定的评级。最后，我们希望为星的总数分配一个值，以便该组件可以用于有 4 星评级、5 星评级、10 星评级等的应用程序。

在书中，我们为`StarRating`使用了一个类组件，为`Star`使用了一个函数组件。`Star`看起来是这样的:

```
const Star = ({ selected = false, onClick = f => f }) => (
  <div className={selected ? "star selected" : "star"} onClick={onClick} /> ); 
```

`Star`有两个属性:`selected`和`onClick`。如果`selected`为假，它将有一个`star`的`className`，这意味着它将是灰色而不是红色。单击时，调用`onClick`方法。

接下来，我们来看看`StarRating`。该组件将呈现来自`totalStars`属性的`Star`个组件的数量。点击时，每一个`Star`都会触发`change`方法，设置`starsSelected`的状态。作为典型的类组件，我们需要在构造函数中处理设置:设置初始状态，然后为`change`事件绑定`this`。

```
class StarRating extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      starsSelected: 0
    };
    this.change = this.change.bind(this);
  }

  change(starsSelected) {
    this.setState({ starsSelected });
  }

  render() {
    const { totalStars } = this.props;
    const { starsSelected } = this.state;
    return (
      <div className="star-rating">
        {[...Array(totalStars)].map((n, i) => (
          <Star
            key={i}
            selected={i < starsSelected}
            onClick={() => this.change(i + 1)}
          />
        ))}
        <p>
          {starsSelected} of {totalStars} stars
        </p>
      </div>
    );
  }
}

render(
  <StarRating totalStars={5} />,
  document.getElementById("react-container")
); 
```

您也可以在 CodeSandbox 上查看:

这看起来很不错，但是我们可以用钩子把它做得更好。

## 用钩子重构

当开始重构这些组件时，我们考虑了几个主要步骤:

1.  弄清楚如何更新`Star`函数
2.  将类组件重构为功能组件
3.  创建一个用于选择星级的挂钩

### 1。弄清楚如何更新`Star`函数

这一步很容易。由于`Star`已经是一个函数，我们根本不需要改变它。感谢 2017 年的 Alex 和 Eve。

```
const Star = ({ selected = false, onClick = f => f }) => (
  <div className={selected ? "star selected" : "star"} onClick={onClick} /> ); 
```

### 2。将类组件重构为功能组件

接下来，让我们重构类组件，使其成为一个函数。我们从`ReactDOM.render`开始。我们想要渲染`StarRating`并给它传递一个属性:`totalStars` :

```
render(<StarRating totalStars={5} />, document.getElementById("root")); 
```

接下来，我们将重构组件`StarRating`，将它`totalStars`作为属性传递:

```
const StarRating = ({ totalStars }) => {
  return (
    <div className="star-rating">
      {[...Array(totalStars)].map((n, i) => (
        <Star
          key={i}
          selected={i < starsSelected}
          onClick={() => this.change(i + 1)}
        />
      ))}
      <p>
        {starsSelected} of {totalStars} stars
      </p>
    </div>
  );
}; 
```

我们需要更新`Star`的`onClick`处理程序，因为`StarRating`函数不能访问`this`。相反，我们将用一个钩子来处理这个问题:

### 3。创建一个用于选择星级的挂钩

钩子允许你在不写类的情况下使用状态。当`StarRating`是一个类时，我们有一个用于`starsSelected`的状态变量和一个名为`change`的方法来设置`starsSelected`的状态。我们将在函数组件中使用相同的状态变量:

```
import React, { useState } from "react";

const StarRating = ({ totalStars }) => {
  const [starsSelected] = useState(0);
  return <div className="star-rating">...</div>; }; 
```

我们使用的钩子叫做`useState`，它允许我们向函数组件添加状态。我们将使用同一个名为`starsSelected`的状态变量，并通过将它传递给`useState`函数将其初始值设置为`0`。换句话说，当组件渲染时，`starsSelected`的初始状态将是`0`。

接下来，我们需要一种方法在给出新的评级时改变`starsSelected`的值。这在类组件中曾经被称为`change`，但是让我们把它描述得更清楚一点，称它为`selectStar` :

```
const StarRating = ({ totalStars }) => {
  const [starsSelected, selectStar] = useState(0);
  return <div className="star-rating">...</div>; }; 
```

然后我们将替换`onClick`中的`change`函数，使用`selectStar`方法:

```
const StarRating = ({ totalStars }) => {
  const [starsSelected, selectStar] = useState(0);
  return (
    <div className="star-rating">
      {[...Array(totalStars)].map((n, i) => (
        <Star
          key={i}
          selected={i < starsSelected}
          onClick={() => selectStar(i + 1)}
        />
      ))}
      <p>
        {starsSelected} of {totalStars} stars
      </p>
    </div>
  );
}; 
```

很圆滑，对吧？我们根本不需要做太多的改变。只需将组件快速重构为一个函数并创建一个钩子。生成的组件代码更少，可读性更好。

这是 CodeSandbox 上的[整件事。](https://codesandbox.io/embed/v0n20v6143)

当钩子在 ReactConf 发布时，人们疯狂地开始尽可能多地使用它们。这是有道理的，因为他们很酷。不过你不需要着急。从重构一些小组件开始，也许是你非常熟悉的组件。在我们的书中和我们的研讨会上，我们已经无数次地提到了这一点。看到这位老朋友带着钩子比以往任何时候都更好看，令人兴奋。