# 不同于 CSS-in-JS 的方法...

> 原文：<https://dev.to/areknawo/a-different-approach-to-css-in-js-58ol>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容。**

如果你是一名 web 开发人员，并且你已经使用过一些 JS 框架(尤其是 React)，那么你可能熟悉 [**CSS-in-JS**](https://cssinjs.org/) 的概念。基本上，这可以归结为通过 JavaScript 创建 CSS 样式表，而不是普通的 CSS。它比像 SCSS 这样的解决方案要好一些，因为它能让你持续获得所有的 JS 优点。它也从整体上简化了你的 CSS 样式和一般的**开发体验** (DX)的管理。

现在，让我们提醒自己 [**TypeScript**](http://typescriptlang.org/) -包含静态类型系统的 JS 超集。这个通过额外的工具、建议和**类型安全**进一步改进了 DX。因此，应该问这样一个问题——如果我们将 CSS-in-JS 和 TypeScript 混合在一起，会发生什么？好吧，我告诉你- [**TypeStyle**](https://typestyle.github.io/) 会发生！所以，请原谅我的这一点，因为我们将会发现，这样的结合能为我们提供什么样的好处，如果它值得你努力，以及如何使用它！

# 想法

首先，让我们后退一步，讨论一下为什么有人会把 TS 和 CSS-in-JS 概念混为一谈。在这里，答案很简单——*因为为什么不！？像真的一样，CSS-in-JS 只是一个一般的概念，显然与 CSS 和 JS 有关，而 TS 只是一个 JS 超集，可以方便地访问其所有底层功能。这就是为什么不做这样的事情是没有意义的。*

更进一步说，这种混合可能带来的好处使它变得更加有趣！CSS-in-JS 的概念和实现它的库，都是为了让 CSS 更...*【可维护】*。你可能知道，他们中的许多人以不同的方式实现它。一种允许你以对象的形式定义你的 CSS 类，另一种以模板文字的形式，还有一些通过提供一个 Babel 插件使整个事情变得更加复杂。不要误解我——所有这些方法都很好，当然这取决于您的用例。但是，它们也有更多的缺点...

几乎所有这些库都缺乏的一点是类型安全。当然，我是说打字稿。大多数都是用普通的 JavaScript 编写的，只有一些部分完整的外部类型。这种状态可能是创建一个合适的静态类型的 API 有多困难的结果，特别是对于 CSS 的 JS 表示。有太多的 CSS 属性和专门的规则(如`@media`)来做这件事。然而，仍然-我们可以尝试！

# 类型样式

那么，什么是类型风格呢？现在你显然知道了——这是一个 CSS-in-JS 库，用 TypeScript 编写。它的主要目标是使 **CSS 可维护**和**类型安全**。也就是说，它还内置了一些非常漂亮的功能。

TypeStyle 与许多 CSS-in-JS 库的不同之处在于，它只在运行时使用。通过使用所有与 CSS 相关的 API(我在[我的上一篇文章](https://areknawo.com/messing-with-css-through-its-javascript-api/)中讨论过这些)，它简单地用 JavaScript 创建所有的样式表，而不是做任何预处理。这样，TypeStyle 就超级*【便携】*。由于其基于运行时的模型和小尺寸( **~6 KB 最小压缩**)，您可以将它换入并准备好！

这个库也是独立于框架的。正因为如此，TypeStyle 试图在比某些库更高的程度上模仿 CSS 设计。当然，这也带来了一些可能的*【缺点】*，比如——最明显的——没有自动前缀和其他后 CSS 的东西。

当然，TypeStyle 最大的特点是它的**类型化**。该 API 在允许 TS 支持的自动完成和代码提示特性方面做得很好。也许 CSS 永远不会是 100%类型安全的，但是这个库很好地将我们今天所拥有的提升到了一个全新的水平。

# 基础知识

因此，在我们进行了一些推理和介绍之后，让我们开始对 TypeStyle API 进行简单的概述。请记住，它并不是一个真正的大库，它的 [**文档**](https://typestyle.github.io/) 已经尽力解释了所有的东西。也就是说，如果你想知道更多，就去看看吧。

```
npm install typestyle 
```

Enter fullscreen mode Exit fullscreen mode

## CSS 类

TypeStyle 最基本的用途是创建简单的 **CSS 类**。

```
import { style } from "typestyle";

const className = style({
    backgroundColor: "red",
    width: 100,
    height: 100
}); 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`style()`函数，我们创建了一个新的 CSS 类，稍后我们可以通过返回的、**散列的**类名来访问它。提供的配置对象可以像任何其他对象一样对待。这包括解构、`Object.assign()`和其他酷的东西。您可以通过向`style()`函数提供任意数量的配置对象来做类似的事情。

```
import { style, types } from "typestyle";

const rect: types.NestedCSSProperties = {
    width: 100,
    height: 100
};

const className = style({
    backgroundColor: "red",
    ...rect
}); // or style({backgroundColor: "red"}, rect); 
```

Enter fullscreen mode Exit fullscreen mode

这种模式的使用将导致我们的样式配置的所有*“组件”*中失去类型安全和 TS 支持。如果您正在使用 TS，并且不希望发生这种情况，您可以在 TypeStyle 提供的类型的帮助下直接指定对象的类型，就像上面的例子一样。

## 嵌套

对类似于`style()`的函数的基本 TS 支持存在于其他多个 CSS-in-JS 库中。使 TypeStyle 与众不同的是这种集成的程度。一个很好的例子就是 TypeStyle 处理**伪类**的方式。看一看:

```
// ...
const className = style({
    backgroundColor: "red",
    ...rect,
    $nest: {
        "&:hover": {
            backgroundColor: "green"
        }
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

该库需要特殊的嵌套属性- `$nest` -以便为不同的伪类和东西提供样式配置。这允许 TypeScript 推断正确的类型，从而为众所周知的伪类提供所有可能的支持。属性也可以用于普通的**嵌套选择器**。但是，请记住，这种用法使您没有 TS 支持，并且在大多数 CSS-in-JS 场景中，带有嵌套选择器的类有点难以管理。

## 帮手

一般来说,`style()`函数就是 TypeStyle 的全部功能。既简单又直观。库的其余部分基本上建立在这个功能之上，增加了额外的**助手函数**和其他有用的工具。

### 媒体查询

这类助手最著名的例子包括`media()`函数，用于类型安全的**媒体查询**。

```
import { style, media } from "typestyle";
// ...
const className = style(
    rect,
    media({minWidth:0,maxWidth:600}, {backgroundColor: "red"}),
    media({minWidth:601}, {backgroundColor: "green"}),
); 
```

Enter fullscreen mode Exit fullscreen mode

`media()`功能是一个**混音**，输出一个正常风格的配置。你可以把它看作是对`$nest`属性的一个很好的替代。

```
// ...
const className = style(
    rect,
    $nest: {
        "@media only screen and (max-width: 600px)": {
            backgroundColor: "red"
        },
        // ...
    }
); 
```

Enter fullscreen mode Exit fullscreen mode

很不错，是吧？对于一些高级用例来说,`$nest`属性可能仍然是必需的。记住，因为我们在 JS/TS 中工作，你可以创建你自己的 mixins，为了给你的主风格配置一些**结构和**外观。

### 动画

就像媒体查询一样， **CSS 关键帧动画**是一个同样*“特殊”*的特性，在 CSS-in-JS 中可能很难使用。对于这种类型，再次提供了很好的助手函数- `keyframes()`。

```
import { style, keyframes } from "typestyle";
// ...
const animationName = keyframes({
  '0%': { color: 'red' },
  '100%': { color: 'green' }
})

const className = style({
    ...rect,
    animationName: animationName,
    animationDuration: '2s',
}); 
```

Enter fullscreen mode Exit fullscreen mode

该函数返回已创建动画的新散列名称，供您以后使用。正是这种直觉让我真正喜欢上了这个图书馆。

### 串联

最后，如果您使用 React 或简单的`className`属性，您可能会喜欢`classes()`助手。它只是**连接**所有提供的类名并返回结果。

```
import { classes } from "typestyle";
// ...
const classStr = classes(className, className2); 
```

Enter fullscreen mode Exit fullscreen mode

# Raw CSS

因此，从上面的例子中可以看出，TypeStyle 提供了一组很好但很小的帮助函数。比如真的——6kb 的库能装多少？反正重点是图书馆不是什么都提供帮手。如果你喜欢的话，你可以使用 mixins，component objects 等等来轻松地创建它。

您现在可能已经猜到，TypeStyle 将所有的类和内容应用到一个**单个样式表**(单个`<style/>`标签)中，这个样式表是在一些 CSS 相关的 Web APIs 的帮助下创建的。当使用 TypeStyle 的 **raw CSS** 函数- `cssRule()`和`cssRaw()`时，这是一个需要记住的重要细节。

```
import { cssRule, cssRaw } from "typestyle";
// ...
cssRule(".red-rect", {
  ...rect
  backgroundColor: "red"
});

cssRaw(`
.green-rect {
  height: 100px;
  width: 100px;
  background-color: green;
}
`); 
```

Enter fullscreen mode Exit fullscreen mode

我不认为这些功能需要深入的解释。首先允许您创建一个带有**自定义字符串选择器**的 CSS 规则，这在某种程度上仍然是类型安全的。另一方面,`cssRaw()`应该只用于加载 CSS 库，即使这样——使用普通的外部 CSS 文件可能会更好。它根本不提供类型安全！

当然，这样的函数非常有用——尤其是当您希望所有的 CSS 都以 CSS-in-JS 的方式编写时。此类功能可与`@import`规则一起使用，其中**位置关系到**。这就是为什么理解 TypeStyle 在单个样式表上工作是如此重要，对于这样的用例，您应该在任何其他 CSS 相关调用之前使用`cssRaw()`，将您的自定义规则放在样式表顶部的**处。**

# SSR

我之前提到过 TypeStyle 是只在运行时使用的。这意味着在默认情况下，它并不基于任何种类的巴别塔插件和其他东西。如果你想证明这不是最佳决策，那么请三思。**性能损失**没有被**注意到**(至少对我来说)，你真的不应该用可维护性来交换性能。但是，如果你不想改变主意，还有一个办法。

TypeStyle 内置了对**服务器端渲染** (SSR)和静态页面生成的支持。由于使用了单个样式表，TypeStyle 提供了一个易于使用的函数- `getStyles()` -来提取它的所有规则。

```
import { style, getStyles } from "typestyle";
// ...
const className = style({
  backgroundColor: "red"
  ...rect,
});

getStyles();
/* Example result:
hashed-class-name {
    height: 100px;
    width: 100px;
    background-color: red
}
*/ 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`getStyles()`函数，您可以轻松地使用所有的类型样式特性——包括散列的 CSS 类名，而不会有任何(甚至是最小的)性能损失。只需将这个调用的结果放入模板文件的`<style/>`标签中，就可以开始了！当然，如果你知道它是如何做到的，你甚至可以非常容易地创建你自己的巴别塔插件。

# 还有更多！

因为我不希望这篇文章是一个文档，而不是一个简单的，初学者友好的教程，我们就到此为止。在 [**官方文档**](https://typestyle.github.io/) 中仍然有一些有趣的特性和陷阱。如果你对这个库感兴趣，我强烈推荐阅读文档——它们写得非常好！但是，即使如此，通过本文中学习的一系列特性，您应该能够轻松地以类型安全、可维护和富于表现力的方式表示大多数 CSS。

# 思想？

那么，**你觉得**的字体怎么样？你喜欢这种有点不同的 CSS-in-JS 方法吗？请在**下面的评论区**让我知道。此外，如果你喜欢这篇文章，可以考虑给**留下一个反应**、**评论**或**对未来帖子的建议**。更多最新内容，在 Twitter 上**关注我****[**我的脸书页面**](http://facebook.com/areknawoblog) 或通过 [**我的个人博客**](https://areknawo.com) 。我希望你喜欢这个，并祝你今天过得愉快！**