# jQuery 到 ES6，如果不需要 JS 框架怎么办…

> 原文：<https://dev.to/joethom/jquery-to-es6-and-what-if-you-dont-need-a-js-framework-1m2n>

*原载于[媒体](https://medium.com/inktrap/jquery-to-es6-and-what-if-you-dont-need-a-js-framework-387b4a043a26)T3】*

几年前，如果您需要在网站上添加一些交互式特性或功能，您可能会选择 jQuery 的以 DOM 为中心的选择器。然而，最近随着“现代 javascript 框架”的兴起，越来越多的开发人员涌向这些工具(这是有充分理由的)。但是，如果你的站点不需要状态和声明式 UI，那该怎么办呢？我们是否应该继续使用 jQuery，或者是否有其他更好的方式，一种 ES6 方式？

[![Intro image](img/399d872e3ae614bb2b7a92f4117251b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EutLqR78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vjmyu4gya7ws2bd7tnsz.png)

我想说的是= { workInProgress: true }这是一项正在进行的工作，更是我们构建过程的高级概述。它适合我们作为代理的特定需求，但可能不适合您自己的需求。

* * *

## 前兆

在我们的静态站点中，在迁移到 ES6 之前，我们将利用 Bootstrap 之类的库，并使用 jQuery 对它们进行相应的定制，以实现更复杂的交互性。然而，过了一段时间后，我们发现我们的 JS 会很快变得臃肿，无结构，难以维护，难以在开发人员之间传递。虽然这些问题不能完全归咎于我们对 jQuery 的使用，但 BEM、SASS 和 Twig 等一些技术鼓励我们代码库的模块化、结构化和可维护性，而 jQuery 却没有，作为一个不断发展的机构，我们希望改变这种情况。

* * *

## 计划

对于从 jQuery 到 ES6 的过渡，我们有一个清晰的想法，所以我们展示了一些关键特性:

*   模块化
    我们想要分解我们的 JS 文件结构，以复制一些更类似于我们的 SASS 设置的东西，将功能分离到单独的文件中，并使用 ES6 import 只通过我们每个项目需要的东西。

*   为了提高跨项目知识和开发人员之间的项目传递，我们希望鼓励功能的共同结构化，至少在项目的开始阶段。

*   有时，我们的构建被传递给客户，客户反过来将它们插入到他们自己的栈中，以添加更多的数据驱动内容。因此，不管客户的堆栈如何，我们的 JS 能够被“挂钩”(*行话是，请*)并相应地更新是很重要的。

*   在 Inktrap，我们还开发了更多数据驱动的 web 应用程序，这些应用程序需要使用 React 或 Vue.js 等框架，并且大量使用 ES6。我们希望确保构建一个静态站点也将利用与我们更复杂的构建相同或相似的方法和思维过程，即使它没有使用框架。

* * *

# 结果

我们决定首先创建一个文件夹结构，并将我们的功能拆分成文件，包括以下内容:

[![functions image](img/68c9cafd053a3a7b4fffe7d2f9022719.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4v10Y8Zh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j1j1h3og1qs0w4uuz655.png)

在`globals.js`中，我们创建了两个全局对象，`FUNCTIONS` & `INSTANCES`，现在我将简单介绍一下:

`FUNCTIONS` :
一个对象，你猜对了——站点中所有的函数处理程序，可以在任何时候从前端调用这些函数处理程序来初始化任何 DOM 元素上的交互性。

`INSTANCES` :
由任何初始化的类实例(我们称之为“**模块**”)组成的对象，包含可以被任何其他 JS 函数访问的方法和回调。

然后，我们为函数处理程序设计了一个通用结构，这里有一个在`dropdownsHandler.js`中使用的例子:

[![dropdownsHandler.js image](img/02a423dc17c7cba4d7adc7c04949d8f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PB1Nzdt---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gpg3nz9xqh66mhhxbxv4.png)

正如你在函数处理器中看到的，我们正在初始化一个新的`dropdown`模块实例。为了给你一个快速的概念，这里有一个模块提供的初始化和公共方法的例子。

[![dropdown.js image](img/ab3a7699b708f0c9b63455dfa872f63c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--alG1QPm4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ma2ro69gwq043al9042t.png)

现在——仅仅初始化一个简单的下拉菜单，上面的内容看起来有点多余。然而，使用这种方法将使我们在“挂钩”(*是的，我知道*)我们的前端系统到其他客户端环境时获得两种重要的能力。

在我开始之前，这里有一个我们的`load.js`文件的快速视图，它处理我们的函数处理程序在页面加载时的整体初始化:

[![load.js image](img/de48813716d7bb79c5dda47aa5447f24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9buSL9FB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nzo2awykozbkriut8d26.png)

使用上面的初始化方法后，我们现在可以从窗口对象访问我们的全局`FUNCTIONS` & `INSTANCES`对象，我们可以在控制台中看到它们包含的内容:

[![console log image](img/4e7252cc07ce1022fef0efd2bc093079.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VJmGKX8r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dwibva0nuebdla614rd2.png)

上面展示了我之前提到的两个重要能力——第一个是我们现在有了一个自定义函数处理程序的列表，可以在任何时候使用:
`window.FUNCTIONS.dropdownHandler(‘.myPassedElement’);`
调用这些函数处理程序，这对于初始化页面加载后呈现的动态内容特别有用。

其次，我们现在也有一个已经初始化的实例列表，每个实例都有自己的方法和回调，可以在我们的 JS 中的其他地方使用，例如:
`window.INSTANCES.dropdowns.helloImADropdown.showDropdown();`

这两种能力在使我们的前端模板更适应任何客户端环境中是必不可少的，因为它们现在可以“挂钩”(*上次我承诺*)到我们 JS 的几乎任何方面。

* * *

# 我们的学问

在我们对几个生产项目实施了这种方法之后，除了前面提到的好处之外，我们还学到了以下几点:

*   模块化带来了更好的可维护性。换句话说，通过将我们所有的功能拆分到定义的文件中，无论何时我们添加新功能或修复错误，我们都可以轻松地将其移植到使用相同系统的所有其他项目中，而不用担心*太多*合并冲突。

*   **改进的跨项目知识**
    如果每个功能都是使用通用的初始化结构构建的，那么开发者之间的交流就更容易了，因为他们已经知道自己在寻找和使用什么了。

*   **消除依赖并改善控制**
    通过移除 bootstrap 和 jQuery 等库，我们不再严重依赖第三方工具，并完全控制我们的客户可以从我们的前端 JS 访问和使用的内容。

*   因为我们不使用 javascript 依赖视图(本质上我们使用的是 HTML、CSS、JS 的基本堆栈),如果由于某种原因我们的 JS 没有加载，用户仍然会收到我们的网站与 HTML 和 CSS 的一些相似之处。本质上，我们添加 JS 作为一个额外的交互层，由我们的标记初始化。

* * *

# 最终的想法，感受&的情绪

现在，上面的许多实践已经被使用过，但是通常是在框架的上下文中，而不是在静态站点中，这是我写这篇文章的想法。我相信在静态网站和成熟的 JS SPA 之间有一个空间需要一些关注，虽然我不认为这是完美的解决方案，但在我看来这是一个开始。

正如我在本文开头提到的，这是对我们构建过程的高度概括，特别是从 jQuery 到 ES6 的过渡以及它带来的好处。我想深入探讨的还有很多，比如我们如何以一致的方式从标记中初始化我们的 JS，我们的 SASS/BEM 设置，甚至可能开源我们的样板文件 repo，但是出于时间的考虑，我想保持这篇文章(相对)简洁。

不过，我确实计划继续报告我们在 JS 不断变化的时代中所学到的东西。

如果您有任何反馈，我们很乐意听到，请在这里留下评论，或在 Twitter 上给我们留言。 [@InktrapDesign](https://twitter.com/InktrapDesign)