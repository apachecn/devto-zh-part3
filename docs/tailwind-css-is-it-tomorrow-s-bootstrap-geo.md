# 顺风 CSS:是明天的 Bootstrap 吗？

> 原文：<https://dev.to/bnevilleoneill/tailwind-css-is-it-tomorrow-s-bootstrap-geo>

[![](img/8e7bad27e4f9911f10c0d9753bb513b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---1yHeQKt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgJkwvC01hwnQZd51ylvp9g.jpeg)

我们正在经历令人惊叹的网络平台和响应式设计的复兴。响应式用户界面大多是用 Bootstrap、Foundation、布尔玛或老式媒体查询等库实现的。

我们已经轻松地使用这些 UI 工具包来实现指令，以更少的代码实现我们所需要的精确的 UI 和适当的响应。但最大的问题是，我们真的做对了吗？

如果有一种方法可以实现响应式 UI，而不受任何 UI 工具包规则的约束，那会怎么样？有没有一种方法既能实现快速响应，又能保持我们定制的用户界面设计？好吧，让我们来看看。

### 什么是顺风 CSS？

根据[官方文档](https://tailwindcss.com/docs/what-is-tailwind)，Tailwind CSS 是一个实用优先的 CSS 框架，用于快速构建定制用户界面。我认为这是一种很酷的方式来编写内联样式并实现一个很棒的界面，而不用编写自己的 CSS 代码。

在我看来，大多数开发人员会发现 Tailwind CSS 有一点令人分心，那就是你的标记看起来比你想象的要繁忙得多。Tailwind 不是第一个实用的 CSS 库，但它是目前最流行的。

[![](img/18c623c22aecb029a54b76acf9d59712.png)](https://logrocket.com/signup/)

### 入门

虽然 CDN 是在项目中导入样式的好方法，但是使用 CDN 版本**无法使用 Tailwind CSS 的许多特性。**为了充分利用顺风的特性，[通过 npm](https://tailwindcss.com/docs/installation#npm) 安装顺风。

#### 1。通过 npm 安装顺风

npm 上有[顺风，可使用 npm 或 Yarn 安装。](https://www.npmjs.com/package/tailwindcss) 

```
# Using npm
npm install tailwindcss --save-dev
# Using Yarn
yarn add tailwindcss --dev 
```

#### 2。创建顺风配置文件

Tailwind 几乎完全是用普通 JavaScript 配置的。为此，您需要为您的项目生成一个顺风配置文件。建议在项目的根目录下创建一个 tailwind.js 文件。CLI 实用程序有助于轻松处理这一问题。

```
# Using npm
npx tailwind init [filename]
# Using Yarn
yarn tailwind init [filename] 
```

如果您是一个有经验的 Tailwind 用户，不需要配置文件中的注释，那么您可以在生成配置文件时使用- no-comments 标志来删除它们。有关设置的更多信息，请查看官方[文档](https://tailwindcss.com/docs/configuration)。

### UI 套件的问题

首先，让我们看看我们一直在做什么，以及我们如何才能更好地为框架增加灵活性。然后，让我们探索为什么使用 UI 工具包可能不是更新信息的最佳选择。

像 Bootstrap 这样的框架已经将组件的创建抽象到了迫使开发人员只使用提供的可用模式的程度。其他 UI 工具包类型的框架也是如此。

有些人可能会说用我们自己的 CSS 覆盖框架是一种选择，但是如果我们覆盖了很多，那么使用框架真的有意义吗？我们将把库拉进来，仍然编写我们自己的代码——这只是需要担心更多的文件，我们甚至没有节省时间。

我发现 Bootstrap 站点的另一个问题是它们看起来几乎总是一样的，所以这抑制了我们将创造力融入开发环境的能力。这是 Tailwind CSS 的优势之一:它能够轻松构建复杂的用户界面，而不会鼓励任何两个站点看起来相同。

### “效用至上”到底是什么意思？

实用优先的库意味着不像 Bootstrap，Tailwind 没有给我们自动预样式的组件。相反，它给了我们一些实用程序类，帮助我们以某种方式设计组件，并允许我们使用这些实用程序类构建自己的类。让我们用两个简单的例子进一步解释这一点。

#### 例 1:简单按钮演示

[https://codepen.io/ekwunoobinna/embed/MRpNQj?height=600&default-tab=result&embed-version=2](https://codepen.io/ekwunoobinna/embed/MRpNQj?height=600&default-tab=result&embed-version=2)
从上面的例子可以看出，用 Tailwind CSS 实现一个按钮组件是多么的容易。

#### 例 2:简单卡片演示

下面的代码是一个用 Tailwind CSS 框架创建的卡片的简单示例——如果你熟悉 CSS，你可能已经能够理解一些关于样式的事情。一开始要记住所有这些可能有点困难，但是一旦你熟悉了语法，你就没问题了。

下面的代码片段有一个容器，它有一个由 shadow-lg 类产生的大屏幕阴影和一个使用 bg-white 实现的白色背景。我们还可以注意到 px-4 和 py-1 py-1 类只是为了帮助在 x 轴和 y 轴上填充消息按钮。

```
<div class="bg-white mx-auto max-w-sm shadow-lg rounded-lg overflow-hidden">
  <div class="sm:flex sm:items-center px-6 py-4">
    <img class="block h-16 sm:h-24 rounded-full mx-auto mb-4 sm:mb-0 sm:mr-4 sm:ml-0" src="https://avatars2.githubusercontent.com/u/4323180?s=400&u=4962a4441fae9fba5f0f86456c6c506a21ffca4f&v=4" alt="">
    <div class="text-center sm:text-left sm:flex-grow">
      <div class="mb-4">
        <p class="text-xl leading-tight">Adam Wathan</p>
        <p class="text-sm leading-tight text-grey-dark">Developer at NothingWorks Inc.</p>
      </div>
      <div>
        <button class="text-xs font-semibold rounded-full px-4 py-1 leading-normal bg-white border border-purple text-purple hover:bg-purple hover:text-white">Message</button>
      </div>
    </div>
  </div>
</div> 
```

这里有一个指向 CodePen 的链接，展示了上面代码的样子:
[https://codepen.io/ekwunoobinna/embed/oOWvEr?height=600&default-tab=result&embed-version=2](https://codepen.io/ekwunoobinna/embed/oOWvEr?height=600&default-tab=result&embed-version=2)

### 使用 Tailwind CSS 有性能优势吗？

默认的顺风配置带有 36.4kb 的 minified 和 g-zipped。与 22.1kb 的 Bootstrap 相比，Tailwind 要重 14.3kb。您可能会想，“就性能而言，这真的是一条路吗？”

原因很简单:Tailwind 预装了许多选项和样式供用户选择，它打包了所有这些变化以减少编写自己的 CSS 的倾向。幸运的是，Tailwind 提供了一些策略，可以用来保持生成的 CSS 小而高效。

#### 限制你的调色板

由于 Tailwind 中所有内置的实用模块都使用了插件系统，所以可以删除大量代码，让插件系统像在 Tailwind 中注册新类一样。

这使得项目中只需要我们实际需要的代码，而忽略其他所有东西成为可能——不像 Bootstrap，其中有许多开销代码。这个升级特性将构建时间从 158 秒缩短到了 8 秒。

我们可以得到我们在一个项目中需要的确切的颜色变化量，就像这样:

```
// ...

module.exports = {
  // ...

  textColors: {
    'black': colors['black'],
    'grey-darker': colors['grey-darker'],
    'grey-dark': colors['grey-dark'],
    'red-dark': colors['red-dark'],
    'red': colors['red'],
    'blue-dark': colors['blue-dark'],
    'blue': colors['blue'],
    // ...
  }
} 
```

> ![Adam Wathan profile image](img/125e0b188aac09c9a0be8f3372fd8180.png)亚当·瓦森@亚当瓦森![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)刚刚对 [@tailwindcss](https://twitter.com/tailwindcss) 构建过程做了很好的 O(n)到 O(1)的性能优化💪🏻
> 
> 你们这些怪物使用 at-apply 做什么都会开心😇2018 年 03 月 01 日上午 00:55[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=969013222471807014)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=969013222471807014)9[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=969013222471807014)99

#### 用 PurgeCSS 移除未使用的 CSS

Tailwind 也用 [PurgeCSS](https://github.com/FullHuman/purgecss) 移除未使用的 CSS，这是一个从项目中移除未使用的 CSS 的工具。它通过简单地将模板中可用的 CSS 类名与所使用的类名进行比较，然后移除未使用的 CSS 来实现这一点。

#### 选择您需要的确切屏幕数量

Tailwind 允许你选择你需要的屏幕尺寸。下面是定义更少的屏幕对输出的影响:

*   5 个屏幕尺寸*(默认)* : 36.4kb
*   4 个屏幕尺寸:29.4kb
*   3 种屏幕尺寸:22.4kb
*   2 个屏幕尺寸:15.4kb
*   1 屏幕尺寸:8.4kb

### 那么 Tailwind CSS 到底有多灵活呢？

你准备好了吗？你确定吗？好了，Tailwind 非常灵活，它允许你添加自己的实用程序，并为你实现它提供了指导。

让我们以一个简单的样式实现为例。正常的顺风设置是一个 CSS 文件，如下所示:

```
 @tailwind preflight;

@tailwind components;

@tailwind utilities; 
```

因此，要覆盖默认的实用程序，我们必须最后导入覆盖，以便首先应用它(常见的 CSS 规则):

```
@tailwind preflight;

@tailwind components;

@tailwind utilities;

.bg-cover-image {
  background-image: url('/path/to/image.jpg');
} 
```

但是如果你正在使用 postcss-import 或者像 Less、Sass 或者 Stylus 这样的预处理程序，将你的工具保存在一个单独的文件中并导入它们将是最好的选择:

```
@tailwind preflight;

@tailwind components;

@tailwind utilities;

@import "leveledup-utilities"; 
```

**注意:**对于响应式布局也可以这样做。只要操作得当，自定义实用程序总是优先于带前缀的实用程序

### 用@apply 提取效用模式

所以，假设你在几个地方重用了某个按钮样式。为同一个组件重新键入相同类型的规范变得有点无聊。不用担心:Tailwind 有办法通过@apply 重用样式。

这里有一个例子:

[https://medium . com/media/2 deca 1 BD 5811488 f 865 fa 73840992209/href](https://medium.com/media/2deca1bd5811488f865fa73840992209/href)

**注意:**`hover:``focus:``{screen}:`效用变量不能直接混合。

### 顺风的生产准备好了吗？

Tailwind CSS 刚刚发布了 1.0.0.beta 版本，以下特性已经可以投入生产了:

#### 扩展默认调色板

这个新版本的 Tailwind 进行了一些升级，通过用新的九色数字颜色系统替换原来的七色、最暗到最亮的颜色系统来帮助灵活使用颜色阴影，其中 100 是最亮的阴影，900 是最暗的阴影。

#### 使用渐进式最大宽度刻度

虽然我们已经有了最大宽度(10 雷姆)的线性比例，但这次新的升级切换到了累进比例，宽度的值增加了，现在有了更多的默认选项，从 9 到 12。

可以通过在配置文件中为自己的项目指定所需的值来覆盖默认的 maxWidth 比例:

```
module.exports = { 
  theme: { 
      maxWidth: { 
      // Your values go here... 
    }, 
  },
} 
```

此外，它还变得更加稳定，可以投入生产。

> ![Adam Wathan profile image](img/125e0b188aac09c9a0be8f3372fd8180.png)亚当·瓦坦@亚当·瓦坦![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)🚀刚刚完整完成[@ tailwindcss](https://twitter.com/tailwindcss)v 1 . 0 . 0 . beta 的发行说明和升级指南！在最终版本发布之前，不会有任何重大变化(只需要完成文档更新)，所以可以放心地开始升级或在新项目中使用它💪🏻
> 
> [github.com/tailwindcss/ta…](https://t.co/wfEDT8XjEZ)2019 年 3 月 20 日下午 19:30[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1108450684783849472)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1108450684783849472)48[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1108450684783849472)287

### 这真是未来吗？

使用 Tailwind CSS 是使用一组实用程序类，让您可以准确地处理您需要的内容。在我看来，这是一种创建用户界面的好方法，可以更灵活地发挥开发人员的创造力。

我非常欣赏的另一个优点是——我相信每个前端开发人员也会欣赏——永远不必担心一个元素的更改会影响另一个相关元素。在编辑器中，不再需要在 HTML 和样式表之间来回切换，也不再需要返回查看其他元素的名称。在我看来，这就是未来。

查看官方[文档](https://tailwindcss.com/docs/what-is-tailwind)并开始体验这种神奇。编码快乐！

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

顺风社:这是明天的引导吗？最早出现在 [LogRocket 博客](https://blog.logrocket.com)上。