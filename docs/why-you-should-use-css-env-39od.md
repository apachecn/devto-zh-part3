# 为什么应该使用 CSS env()

> 原文：<https://dev.to/bnevilleoneill/why-you-should-use-css-env-39od>

[![](img/77aea7da2dd93b0581b392a7ef29392e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3c5R_QcG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgGeiKQHdTkiE7MJWpaDbcw.png)

难以保持共享的 CSS 和 JavaScript 值的更新和同步？更新共享值时，你是否面临看似随机的 bug？在本文中，您将了解如何使用即将推出的 CSS env()特性和 PostCSS 插件在 CSS 和 JS 之间共享相同的变量，并将它们存储在一个文件中。

这里有一个你可能已经面对过的常见场景:

*   *你有一个相对复杂、响应迅速的设计来实现*
*   单独的 CSS 选择器和 calc()不能影响设计，所以你求助于你信任的朋友 JavaScript
*   *要解决这个问题，你需要从 JavaScript* 访问 CSS 值(例如容器尺寸、间距值)

您现在面临一个选择，是单独用 JavaScript 管理变量，还是使用内联样式来应用样式？

或者你在两个地方管理变量，CSS 和 JS 文件？

你进退两难。两种解决方案都不令人满意。

一方面，通过应用内联样式，您放弃了用任何其他方法覆盖样式的机会。现在，您必须始终使用内联样式来覆盖您应用的特定声明。

在下面的例子中，MyComponent 将总是“红色”的，字体大小为 2，因为内联样式优先于应用于组件的类名:

```
.my-component {
  font-size: 1rem;
  color: blue;
} 
```

*内联样式优先*

另一方面，管理两个不同文件中的变量会使您面临维护风险。如果需要更改值，就有可能只在其中一个文件中更新值，导致应用程序以奇怪的方式中断。

但是还有一个办法！

> ![unknown tweet media content](img/05bf50adc6d3c876cbae952c2067b84a.png)![Harry Nicholls profile image](img/ac7ebcb91f5cc79b87134bbe955a88cf.png)Harry Nicholls@ harrynicholls![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)使用 CSS 环境变量可以在 CSS 和 JS 之间共享变量。无需担心在多个地方更新变量或使用内联样式。
> 用[#碳](https://twitter.com/hashtag/Carbon)打造，由[@ dawn _ labs](https://twitter.com/dawn_labs)
> [# web development](https://twitter.com/hashtag/webdevelopment)[# CSS](https://twitter.com/hashtag/CSS)2019 年 3 月 10 日下午 17:07[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1104790799508819969)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1104790799508819969)2[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1104790799508819969)5

Allow me to introduce [CSS environment variables](https://developer.mozilla.org/en-US/docs/Web/CSS/env), and show you how you can share variables between CSS and JS, while storing the values in a single place.

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 问题

下面是我最近遇到的一个更具体的问题。

比方说，我正在开发一个名为 Color Explorer 的应用程序，它允许用户查看颜色列表并选择一个要放大的颜色。

这是最初的版本:

[![](img/6f2344bac61a634e90aeef93e34eca66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NHdHvnfk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AxYBS6HaB4qfi1YpZ.gif) 

<figcaption>颜色资源管理器与溢出颜色列表</figcaption>

看起来不错，有一个颜色列表，您可以单击小样本来更改大样本中显示的颜色。

但是我不喜欢你必须向下滚动才能看到所有的颜色，我希望它们被限制在一行中。

最简单的解决方案是对颜色列表应用 overflow: hidden:

[![](img/e0df2be4ee8d9e84fa14807835117ef9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SSXo9CeI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A9QAMYIJ8SHS3TbUL.png) 

<figcaption>颜色资源管理器带溢出:hidden 应用于 ColorListcomponent</figcaption>

看起来不错，但是有个问题。

即使溢出的样本在视觉上是隐藏的，我仍然可以使用键盘来访问它们:

[![](img/4f268f943337193652700924bfe46c77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2jQb-W9s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AEdUChZ6GmA4NgEly.gif) 

<figcaption>视觉上隐藏的色板可以通过标签按钮</figcaption>

访问

这是一个问题。

我需要我们的朋友 JavaScript 的一点帮助来解决这个问题。

我要做的是测量颜色列表容器，计算一行可以容纳多少颜色样本，并隐藏和禁用所有其他颜色样本。

要做到这一点，我需要知道颜色列表容器有多宽，每个颜色样本有多宽(包括边距)，以及在颜色列表容器的一行中有多少个颜色样本可以并排放置。

我不能从 CSS 中这样做，因为颜色列表容器是响应性的:

[![](img/648e4a3825677b97c6c1192419dded74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4QKERX3g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AQyxCp6FcjEtbN59y.gif) 

<figcaption>期望调色板的响应行为</figcaption>

色板的宽度是固定的，现在它们的宽度是通过 CSS 设置的，那么我们如何从 JS 访问这些信息呢？

### 解决方案 1:冒险的方式

最容易实现的解决方案是将 ColorSwatch 宽度变量存储在 JS 中。

只需将值复制粘贴到 JS 文件中，像这样:
[https://codepen.io/lukelogrocket/embed/BEGNwM?height=600&default-tab=result&embed-version=2](https://codepen.io/lukelogrocket/embed/BEGNwM?height=600&default-tab=result&embed-version=2)
*初始版本的 ColorSwatchSelector*

(**注意:** Measure 取自一个名为 [react-measure](https://github.com/souporserious/react-measure) 的库，在本例中，onResize 事件允许您访问被引用的 HTML 元素的维度

酷！那有效！

这是一个简单的解决方案，但很脆弱。

当您或团队中从未处理过组件的其他人必须更新颜色样本宽度时，会发生什么情况？宽度值只在一个地方更新的风险很大，很可能 CSS 文件和应用程序会崩溃。开发人员需要一段时间来理解为什么应用程序坏了，以及如何修复它。

您可以通过使用 CSS 环境变量来避免这种风险。

### 解决方案 2: CSS 环境变量

Enter，CSS env()。

这是 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/env) 的描述:

> env() CSS 函数可用于将用户代理定义的环境变量的值插入到 CSS 中，其方式与 var()函数和自定义属性类似

本质上，您可以定义全局变量，这些变量可以在属性值可以使用的任何地方使用，包括媒体查询。

CSS env()还不是官方 CSS 规范的一部分，但是 [env()规范](https://drafts.csswg.org/css-env-1/#env-function)在“编辑草案”中，并且有[对它的重要浏览器支持](https://caniuse.com/#feat=css-env-function)，所以一旦所有的细节最终确定，它将成为 CSS 规范的一部分。

你可以查看草案规范，但是 TL；DR:关于如何在 CSS 中访问环境变量已经有了共识，但是目前还没有办法定义或加载它们。

然而，现在您可以通过一个简洁的 PostCSS 插件来使用 CSS env()。

首先，你需要添加 [PostCSS](https://github.com/postcss/postcss) 和 [postcss-env-function](https://github.com/jonathantneal/postcss-env-function) 插件到你的项目中。

(如果你使用的是 create-react-apps，那么 PostCSS 已经安装好了，在 [postcss-env-function GitHub 页面](https://github.com/jonathantneal/postcss-env-function)上有特殊的配置说明。)

您还需要创建一个名为 css-env-variables.js 的文件，如下所示:

```
module.exports = {
  environmentVariables: {
    '--color-swatch-size': '10rem'
  }
}; 
```

*在 JS 中存储 CSS 变量*

这就是你的基本设置！您现在可以像这样开始使用 CSS env():

```
.color-swatch {
  height: env(--color-swatch-size);
  width: env(--color-swatch-size);
} 
```

*使用 env()访问 CSS 环境变量*

很简单，是吧？

您可以添加任何您需要的变量，您在检查器中唯一能看到的是实际值。

即使现在在 JavaScript 文件中管理 CSS 变量，它们也没有多大帮助，因为每个值都包含了单位。因此，您仍然需要在两个单独的文件中维护 JS 和 CSS 变量。从技术上讲，您可以将 CSS env 变量导入到组件中。试试看！

要使用它们，你必须弄清楚如何从值中去掉单位，但是你真的认为这是你的应用程序应该担心的事情吗？

没有。

我们将在下一节讨论解决方案。

### 如何使用 JS env 变量

我和我的团队在当前项目中开始使用的模式是在一个单独的 JS 文件中定义数值，并将它们导入 css-env-variables.js 来构造 css 变量。

这样，我们可以将 JS 值直接导入到任何其他需要它们的 JS 文件中，并使用相同的值来构造 CSS 环境变量。

```
module.exports = {
  colorSwatchSize: 160; // px
}; 
```

*将共享值提取到单独的 JS 模块*

我们这里只有数值，单位总是像素，因为它们在 JS 文件中更容易处理，我们可以将它们直接导入任何 JS 文件。

```
import { colorSwatchSize } from 'path/to/your/js-env-variables'; 
```

*像其他导入一样导入共享变量*

现在我们快到了！

您可能已经注意到 js-env-variables.js 是一个 CommonJS 模块，some-other-file.js 是一个 ES6 模块。为什么不同？

因为 js-env-variables.js 没有被传输。在 transpilation 发生之前，PostCSS 插件使用这个文件，所以我们需要把它写成一个 CommonJS 模块，以便它可以使用。

然后，您可以在 CSS 环境变量文件中使用这些值，如下所示:

```
const { colorSwatchSize } = require('path/to/your/js-env-variables');

module.exports = {
  environmentVariables: {
    '--color-swatch-size': `${colorSwatchSize}px`
  }
}; 
```

*导入您的 JS 变量，创建 CSS 变量，并通过 env()* 导出以访问它们

厉害！现在你只需要维护一个地方的价值观，真理的源泉。

这有效地消除了忘记更新该变量的任何实例的风险，因为它只定义了一次。

### 我们可以做得更好！

我会给你更好的。

上面的设置使用了像素，但我最初在 CSS 中使用的是“rem ”,我想再次使用它。

如果您想设置这个系统为您的 JS 文件提供像素值，为您的 CSS 提供 rem 值，还需要几个额外的步骤:

1.  系统需要知道你的根字体大小
2.  你需要一个 px 到 rem 的转换函数

你可以用与上述相同的方法定义根字体大小:

```
const {
  colorSwatchSize,
  rootFontSize
} = require('path/to/your/js-env-variables');

module.exports = {
  environmentVariables: {
    '--color-swatch-size': `${colorSwatchSize}px`,
    '--root-font-size': `${rootFontSize}px`
  }
}; 
```

*通过 CSS env()管理根字体大小*

只要确保在你的主 CSS 文件中应用它:

```
html {
  font-size: env(--root-font-size);
} 
```

*不要忘记使用 env()* 设置根字体大小

现在你可以创建一个 px 到 rem 的转换函数，并在你的 CSS 环境变量文件中使用它:

```
const {
  colorSwatchSize,
  rootFontSize
} = require('path/to/your/js-env-variables');
const { getPxToRem } = require('path/to/your/px-to-rem');

const pxToRem = getPxToRem(rootFontSize);

module.exports = {
  environmentVariables: {
    '--color-swatch-size': pxToRem(colorSwatchSize),
    '--root-font-size': `${rootFontSize}px`
  }
}; 
```

*将 px 转换为 rem*

您应该能够看到您的- color-swatch-size 变量现在处于 rem 中，并且更改根字体大小值会影响它。

这是一个简洁的小设置，可以为您节省许多调试时间。

### 额外收获:如果我用的是 TypeScript 怎么办？

如果您使用 TypeScript，有几个问题。

您可能很难将 JS 环境变量和 px-to-rem util 导入 CSS env 变量文件。

您可以通过将这些文件设置为它们自己目录中的独立模块以及类型定义来解决这个问题。

您的目录结构应该如下所示:

```
src
|-css-env-variables.js
|-js-env-variables
| |-index.d.ts
| |-index.js
|-px-to-rem
|-index.d.ts
|-index.js 
```

您不需要更改 JS 文件的内容，只需将这些文件添加到/js-env-variables:

```
export const colorSwatchSize: number;
export const rootFontSize: number; 
```

*/js-env-variables 的内容*

并将这些添加到/px-to-rem:

```
export const getPxToRem: (rootFontSize: number) => (pxValue: number) => string; 
```

*/px-to-rem 的内容*

确保将 js-env-variables.js 和 px-to-rem.js 的名称改为 index.js，并将它们放在各自的目录中。TypeScript 编译器应该对此感到满意。

这样做让编译器知道这些模块的存在，并给它一些关于它们类型的信息。

在 Netlify 上试用最终版本的[颜色浏览器。](https://infallible-swartz-e23661.netlify.com/)

### 结论

我们在这里做的是把一个脆弱的、不干燥的系统，变得更干燥、更坚固。

我们发现需要在 CSS 和 JS 之间共享变量，并发现最简单的解决方案(将相同的变量存储在两个不同的文件中)在变量需要改变时可能会失效。

现在，您拥有了在 CSS 和 JS 之间共享任何变量的工具，只需在一个地方根据需要更新值。这种解决方案提供了一种更健壮的共享变量的方式，并且当变量值改变时不太可能中断。

我在这里展示了一个简单的解决方案，只有一个共享变量。设置一个简单的应用程序需要花费相当多的精力，但这只是一个例子。在一个更大、更复杂的项目中尝试一下，我想你会发现这是值得的。

不再有寻找和替换的狩猎。不再有忘记更新某处元素高度实例的困扰。而且不会再出现随机 bug，至少不会和你这样存储的 CSS/JS 变量有关。

如果你还不知道，我强烈推荐你在需要的地方使用这个设置来共享 CSS 和 JS 变量。未来-你会感激你所做的。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[为什么你应该使用 CSS env()](https://blog.logrocket.com/why-you-should-use-css-env-9ee719ce0f24/) 首先出现在[的博客](https://blog.logrocket.com)上。