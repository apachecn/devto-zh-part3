# 我如何以及为什么在顺风中使用情感

> 原文：<https://dev.to/lbayliss/how-and-why-i-use-emotion-with-tailwind-4elj>

*注 2.0:* 由于参考的许多工具和框架发生了很多变化，这篇文章还没有特别成熟。我打算用一个新的示例项目和一个新的帖子来更新这个，但一切都在变化，速度快得我跟不上。请记住，如果你继续下去，你会发现很多事情不再是达到同样结果的最佳途径。

注意: Tailwind 有了一个新的主要版本，它改变了下面的许多内容，如果你对我如何适应升级感兴趣，请告诉我！

## 我最喜欢做 CSS 的方式

我想分享我目前在 react 项目中最喜欢的处理 CSS 的方法。我稍微提到了我是如何使用这个解决方案的，以及最初为什么需要它。我还包含了一些代码片段来展示不同的库和工具是如何一起使用的。

## 问题

我和一个小型开发团队一起工作；其中大部分主要是。NET 开发人员。当我们开始构建我们的最新项目时，我们希望确保我们可以做一些事情。

*   尽可能快地富有成效，并且持续尽可能长的时间。
*   花更少的时间学习技术，花更多的时间解决问题。
*   尽可能保持一致的造型。

这对我们来说意味着我们需要尽快适应堆栈的两端。至少足够舒适，这样我们从一开始就能有所作为。

我们最担心的不是让团队的一部分人学习 JavaScript 和 React，而另一半人学习。NET 核心，而是我们如何处理我们的 CSS。因为 CSS 很难。

## 解

我们的解决方案是 JS 中的 CSS。我不会在这里深入讨论 JS 中的 CSS。如果你对这个想法很陌生，并且对它很好奇，这篇文章是一篇很棒的文章。

具体来说，我们把范围缩小到使用[情感](https://emotion.sh/)和[顺风](https://tailwindcss.com/docs/what-is-tailwind/)以及一些[巴别塔](https://www.npmjs.com/package/babel-plugin-tailwind)魔法让他们成为最好的朋友。

### 为什么情绪激动

*   少了一个构建步骤。
*   在 JavaScript 中感觉最自在
*   直接用 JavaScript 动态改变样式。

建立一个构建过程是一件痛苦而无趣的事情。在 JS 中使用 CSS 意味着我们不需要担心设置 CSS 预处理器；使用情感意味着我们所有的风格都是和 JavaScript 一起构建的。因为样式变成了代码的一部分，我们可以更少地担心将未使用的 CSS 捆绑到我们的项目中，因为只有使用过的 CSS 应该被包括在内。

用 JavaScript 编写我们的风格对我来说更像是在家里。尽管 Emotion 实际上仍然与编写普通的旧 CSS 一样，但在构建新的组件或视图时不必在多个文件之间跳转仍然很好。将所有内容包含在一个文件中，以及 JS 中 CSS 的窄范围特性，意味着在任何时候都更容易关注组件的所有逻辑和样式。

在实践中:

```
.button {
  padding: 10px;
  border-radius: 5px;
  background-color: blue;
  color: white;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
import * as React from 'react';

const Button = (_props) => {
    return <button className="button">Click Me</button>;
};

export default Button; 
```

Enter fullscreen mode Exit fullscreen mode

变成:

```
import * as React from 'react';
import { css } from '@emotion/core';

const buttonClass = css`
  padding: 10px;
  border-radius: 5px;
  background-color: blue;
  color: white;
`;

const Button = (_props) => {
    return <button className={buttonClass}>Click Me</button>;
};

export default Button; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用样式组件(我的首选方法)，我们会得到这个:

```
import * as React from 'react';
import styled from '@emotion/styled';

const Button = styled.button`
    padding: 10px;
  border-radius: 5px;
    background-color: blue;
  color: white;
`;

export default Button; 
```

Enter fullscreen mode Exit fullscreen mode

使用情感很快被证明是为组件构建动态风格的强大方法。我们不再需要为不同的组件状态编写单独的类。我们可以根据组件状态或道具直接修改我们的样式。

```
import * as React from 'react';
import styled from 'emotion/styled';

const Button = styled.button`
    background-colour: ${props => props.isPrimary ? 'blue' : 'green'};
    color: white;
`;

export default Button; 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么顺风

*   速记更容易记住。
*   在简单的事情上节省时间。更多时间做有挑战性的事情。
*   一致性。

我们决定使用 Tailwind 的最大原因是因为它使我们的开发人员可以编写 CSS，这些开发人员几乎没有为 web 构建界面的经验。至少对于像 react 这样的现代框架来说是这样。

能够使用自我描述和易于记忆的类名意味着我们的开发人员可以不用了解太多 CSS 就能写出风格。这意味着他们在构建简单组件时需要考虑的事情更少，节省了(尽管很短)担心更大问题的时间。

写这个:

```
const button = css` ${tw('rounded text-white bg-blue')};
`; 
```

Enter fullscreen mode Exit fullscreen mode

相当于这样写:

```
const buttonClass = css`
    border-radius: 0.25rem
    color: #fefefe;
    background-color: #7070ea;
`; 
```

Enter fullscreen mode Exit fullscreen mode

虽然这是一个相对简单的例子，但是这个 button 类的 Tailwind 方法根本不需要考虑太多。如果想让按钮变圆，我只需添加`rounded`。如果我想要一个蓝色的背景，我只需添加`bg-blue`。事实证明，这是一种非常快速的构建表示组件的方法。它的工作方式和你对`::before`和`:hover`的预期一样。

```
const buttonClass = css` ${tw`bg-purple`} :hover { ${tw`bg-purple-lighter`} }
`; 
```

Enter fullscreen mode Exit fullscreen mode

让 tailwind 处理这么多 CSS 基础知识的另一个好处是，只要我们始终如一地使用 tailwind，样式就会非常一致。我们所有的颜色和预期间距等都由顺风管理。如果我们像预期的那样使用了 tailwind，这意味着我们应该在整个应用程序中保持一致性，并且能够在一个地方(tailwind.js)改变这些颜色和值，并让它立即在整个应用程序中传播。

### 通天塔宏魔

我敢肯定，乍一看，你会看到`tw`的以下用法，并感到有点困惑。如果你错过了，这里又是:

```
consst Button = styled.button` ${tw`bg-purple`} `; 
```

Enter fullscreen mode Exit fullscreen mode

这就是巴别塔魔法发挥作用的地方。使用非常酷的[巴别塔宏](https://babeljs.io/blog/2017/09/11/zero-config-with-babel-macros)，我们可以使用`tailwind.macro`包将这个巴别塔工具直接导入到我们想要的文件中。如果你想知道什么是宏以及它们是如何工作的，你可以看看这个视频。这让我们可以在 emotion 模板的文字字符串中使用 tailwind 类名，它被编译成它们所代表的 CSS。

## 担忧

我并不认为这是完美的。我确实觉得，试图用 tailwind 混淆快捷方式背后的大量 CSS 会使调试样式变得更加困难，并且对于不熟悉工具的开发人员来说，几乎不可能知道到底发生了什么。

我发现这种方法会增加我的组件文件的体积。声明各种样式的组件只使用一次往往会导致一些长度的文件。我通常会尝试将我发现自己跨多个组件重新实现的样式化组件移出到一个模块中。

## 最后的想法

它绝不是学习 CSS 的替代品，因为更复杂的样式需要编写普通的 CSS，但它确实使它更容易理解。一旦掌握了 tailwind 类名，构建组件和页面的速度就会快得离谱；我发现自己很快就屏蔽掉了所有的东西，只需要回头调整一些小的东西，这大大提高了我的开发速度。

编辑:我已经删除了这个存储库。它已经过时，因为顺风和顺风组件包都已更新。我希望很快创建一个新的最新的例子，以及一个新的帖子，展示如何设置更深入的工作。
我有一个用 TypeScript 和 Next.js 创建的“入门”库，如果你感兴趣，你可以在我的 GitHub [这里](https://github.com/lpbayliss/typescript-emotion-tailwind-nextjs)查看。

当然很想听听你对这种方法的看法，或者你有什么类似的选择！