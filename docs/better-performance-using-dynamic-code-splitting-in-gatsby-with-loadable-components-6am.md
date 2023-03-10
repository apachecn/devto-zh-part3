# 在具有可加载组件的 Gatsby 中使用动态代码分割可以获得更好的性能

> 原文：<https://dev.to/itmayziii/better-performance-using-dynamic-code-splitting-in-gatsby-with-loadable-components-6am>

## 前言

我在工作和个人项目中使用 Gatsby，因为我相信就开发人员的效率和给客户增加的价值而言，它是目前最好的工具。让我继续使用 Gatsby 的原因是，他们真正关注的是[性能](https://www.gatsbyjs.org/docs/performance/)，我们都知道在留住用户方面[性能很重要](https://developers.google.com/web/fundamentals/performance/why-performance-matters/)。尽管《盖茨比》令人惊叹，但它并没有完全把表演从我们的盘子里拿走，所以我们再也不用担心它了。作为开发人员，我们应该在每次代码和内容更改后测试我们网站的速度，因为没有工具能够为我们处理世界上的所有边缘情况。《盖茨比》和其他网站都是现成的，但是我们的工作就是不要把它弄糟。在这篇文章中，我想与你分享一个案例，Gatsby 本身不足以处理我们的性能需求，以及我们如何通过不断测试和进行增量更改来解决这个问题。

## 我们面临的性能问题

在我的工作中，我们主要使用两种测试工具来衡量我们的网站性能。

1.  [灯塔](https://developers.google.com/web/tools/lighthouse/)
2.  [网页测试](http://webpagetest.org/)

在 Lighthouse 中，我们的网站得分在 70 分左右(满分为 100 分),有两点需要改进

1.  减少 JavaScript 执行时间
2.  最小化主线程工作

在网页测试中，我们的网站有一个非常高的时间，直到页面被认为完全加载，高加载时间是不好的。我说的“高”是相对于我们习惯于看到的相同网站的性能而言的。关于这个 Web 页面测试工具的一个有趣的事情是，您可以阻止某些 HTTP 请求的发生，这是测试某个请求的出现是否是性能问题的原因的一个非常方便的方法。事实证明，在屏蔽了 gatsby 在页面上生成的 javascript 文件后，我们网站的加载时间缩短了一半！

我们从这两个测试工具中得出的结论是，我们的 javascript 脚本的下载、解析和执行时间太长了。

## 理解为什么盖茨比辜负了我们

事实上，Gatsby 没有让我们失望，但是 Gatsby 为代码拆分提供的开箱即用的解决方案让我们失望了。Gatsby 在这里提供了一篇非常深入的文章，介绍他们如何处理代码拆分[，所以我不打算花太多时间来讨论它。](https://www.gatsbyjs.org/docs/how-code-splitting-works/)

### 动态页面才是真正的问题

我相信我们正在以一种非常独特的方式使用 Gatsby，我们有一个定制的 CMS / design 系统来提供 Gatsby 数据以创建静态页面。我们的 CMS 将页面分成不同的部分，我们称之为*模块*。

[![website with modules outlines](img/26287237107ca6668cfc2b37e2b8b69d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--smEZFHe6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/65mc3o4lm1h0e7dnushr.jpg) 
红线分隔我们网站上的模块，CMS 中的内容编写者可以编写任何这些模块的页面，这意味着在 Gatsby 端，我们必须有这样的代码:

```
export default function Page ({pageFromCMS}) {
  return pageFromCMS.modules.map((module) => {
    const Module = findModuleComponent(module.id)
    return <Module module={module}/>
  })
} 
```

这不是真正的代码，但它很好地说明了我们试图实现的目标。这个想法是，我们只是想采取的模块，CMS 有任何给定的页面和循环，以动态地把它们放在页面上。

这段代码的问题在于，在上面这个名为`findModuleComponent`的函数中，我们必须做一些事情，比如:

```
import ModuleOne from './module-one'
import ModuleTwo from './module-two'

const modules = {
  'moduleOne': ModuleOne,
  'moduleTwo': ModuleTwo
}

export function findModuleComponent (moduleId) {
  if (!modules.hasOwnProperty(moduleId)) {
    throw new Error(`Module ${moduleId} does not exist`)
  }

  return modules[moduleId]
} 
```

从这篇文章的标题中，您是否发现了这个问题以及它与代码分割的关系？

### 基本了解代码拆分

如果在一个文件的顶部有两个`import`语句，Gatsby / Webpack 会在构建期间将这些导入捆绑到一个 javascript 文件中，并生成类似于`https://www.dumpsters.com/component---src-templates-page-js-123eb4b151ebecfc1fda.js`的东西。

### 汇集一切

我们要求 CMS 在任何页面上都有任何模块，这迫使我们在 Gatsby 端动态呈现这些模块。为了动态渲染任何模块，我们必须有一个模块名称映射来反应组件，这迫使我们将所有的反应组件放在同一个文件中。将所有这些导入放在同一个文件中的行为使得 Gatsby/Webpack 认为每个模块/导入在每个单独的页面上都是需要的，因此对于我们的页面特定代码来说，根本不存在代码分割。这是一个真正的问题，因为我们可以轻松地拥有总共 100 个模块，而任何给定的页面可能只使用其中的 10 个，所以我们的页面上有很多不需要的 javascript。

## 解决问题

我们需要一种方法，只导入任何给定页面所需的模块，而不牺牲 CMS 的动态特性。引入 dynamic [进口 react 提到的](https://reactjs.org/docs/code-splitting.html#import)还有 [Webpack](https://webpack.js.org/guides/code-splitting/#dynamic-imports) 。现在动态导入的问题是它依赖于不支持服务器端渲染的 [React.lazy](https://webpack.js.org/guides/code-splitting/#dynamic-imports) 。我们绝对需要服务器端渲染，这是我们选择使用 Gatsby 静态渲染 HTML 页面的另一个重要原因。React 承认了`React.lazy`的这种局限性，他们建议现在使用[可加载组件](https://github.com/smooth-code/loadable-components)来解决这个问题。

### 在 Gatsby 中实现可加载组件

如果你按照可加载组件的文档去做，当你进行到第三步，也就是关于如何设置应用程序的服务器端时，你可能会很快感到困惑。这一步让人摸不着头脑，因为盖茨比已经替你打理这些事情了！Gatsby 本身负责进行服务器渲染，您不需要覆盖它来使可加载组件工作。相反，如果你只遵循[文档](https://www.smooth-code.com/open-source/loadable-components/docs/server-side-rendering/)中的前两步，那就足够开始了。

#### 第一步

你将需要使用一个自定义的巴别塔插件，所以你需要覆盖盖茨比默认的插件，如这里所述。

*.babelrc*

```
{  "plugins":  [  "@loadable/babel-plugin"  ],  "presets":  [  [  "babel-preset-gatsby",  {  "targets":  {  "browsers":  [">0.25%",  "not dead"]  }  }  ]  ]  } 
```

确保安装`@loadable/babel-plugin`和`babel-preset-gatsby`

#### 第二步

您需要添加一个自定义的 webpack 插件。

*盖茨比-node.js*

```
const LoadablePlugin = require('@loadable/webpack-plugin')
exports.onCreateWebpackConfig = ({ stage, getConfig, rules, loaders, plugins, actions }) => {
  actions.setWebpackConfig({
    plugins: [new LoadablePlugin()]
  })
} 
```

再次确保安装`@loadable/webpack-plugin`和`@loadable/component`

#### 修改我们的代码

现在我们有了可加载的组件，让我们使用它的动态导入能力。

```
import loadable from '@loadable/component'

export default function Page ({pageFromCMS}) {
  return pageFromCMS.modules.map((module) => {
    const moduleFileName = findModuleFileName(module.id)
    const ModuleComponent = loadable(() => import(`../modules/${moduleFileName}`))
    return <ModuleComponent module={module}/>
  })
} 
```

如果我们现在停止，我们将大部分代码分割发生在模块级别，因此我们不会在页面上包含一堆不需要的 javascript。但是这样的代码有一个问题。
将会发生的情况是:

1.  静态 HTML 将呈现给用户。
2.  React 将自己水合到静态 HTML 上
3.  您当前的 DOM 将被 React 销毁，因为动态导入需要时间来解决
4.  一旦动态导入实际加载了所需的 javascript 文件，这些模块就会被添加回页面。

这有一个讨厌的效果，屏幕上有内容，它消失，然后重新出现，这是一个可怕的 UX。为了解决这个问题，我们做了一些聪明/巧妙的事情(我会让你决定)。本质上，可加载组件库允许您将后备内容指定为道具，直到它能够加载 javascript 文件。我们不想使用 loading spinner，因为它仍然会刷新内容，相反，我们知道 HTML 已经静态地呈现在页面上，所以我们用一个`document.querySelector`获取该模块的 HTML，然后将其指定为后备内容，直到加载了该模块的 javascript。

这篇文章有点长，所以我要和你分享一些最终解决方案的伪代码/真实代码。

```
import loadable from '@loadable/component'

return page.modules.map((module, index) => {
  const { moduleFileName, shouldLoadJavascript } = retrieveModulePath(module.id)
  if (isServer()) {
    // The server should always render the module so we get the static HTML.
    // RENDER YOUR MODULE
  }

  const wasUserPreviouslyOnSite = window.history.state
  const htmlEl = document.querySelector(`[data-module-index="${index.toString()}"]`)
  if (htmlEl && !shouldLoadJavascript && !wasUserPreviouslyOnSite) {
    // These modules do not require javascript to work, don't even load them
    // RENDER THE STATIC HTML ONLY HERE - something like <div dangerouslySetInnerHTML={{ __html: htmlEl.outerHTML }}></div>
  }

  const fallback = htmlEl && htmlEl.outerHTML ? <div dangerouslySetInnerHTML={{ __html: htmlEl.outerHTML }}></div> : null
  // RENDER THE MODULE NORMALLY HERE WITH THE FALLBACK HTML SPECIFIED
}) 
```

上面的代码为我们完成了许多不同的事情:

1.  动态导入代码以实现更好的代码拆分
2.  允许我们选择不导入不需要 JS 就能工作的模块的代码。
3.  防止出现任何内容闪烁。

## 结论

有时你必须超越我们的工具所能提供的东西，这没关系。盖茨比是一个优秀的工具，我打算用很长一段时间，但它需要一些超级权力添加到它与可加载组件。当我们实现了类似这样的代码时，我们看到总共从我们的网站上删除了大约 200KB 的 javascript，是的，当使用 lighthouse 和 web 页面测试时，我们看到了页面速度的提高。

我知道我在上面留下了一些开放的代码，但我真的不能分享更多，因为这是一个公司项目。如果你有任何问题，请随时联系我，我会尽可能地指导你，而不会逐字逐句地给你解答。

在 dev.to 和 [twitter](https://twitter.com/iTMayzIII) 上的任何关注都会受到感谢！

José Alejandro Cuffia 在 Unsplash 上拍摄的封面照片