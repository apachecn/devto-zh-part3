# 我对 JavaScript transpiler 的愿望清单

> 原文：<https://dev.to/stereobooster/my-wishlist-for-javascript-transpiler-3080>

很久以前我就想过这篇文章，但不确定它的形式。所以我写了[前端开发的下一件大事是什么？](https://dev.to/stereobooster/what-is-the-next-big-thing-in-frontend-development--4nm6)。[最近丹·阿布拉莫夫的帖子](https://overreacted.io/my-wishlist-for-hot-reloading/)给了我这个形式的灵感。(别称:偷这个想法...)

## 前言

### ECMAScript

当我们使用现代 JS(准确地说是 [ECMAScript](https://www.ecma-international.org/publications/standards/Ecma-262.htm) )时，我们必须处理该语言的不同版本，比如 ES5、ES6(又名 ES2015)、ES2016、ES2017、ES2018。这些版本可以定义

*   非常大的语言变化，比如新的操作符(例如,`...` spread 操作符)，这将需要翻译
*   以及一些非常小的变化，比如引入一个新的函数(例如`Array.prototype.includes`),可以很容易地用 polyfilled 填充

### Web API

ECMAScript 并不是唯一一个定义你在做 web 开发时必须处理的标准。Web API 有一大堆[，比如 DOM、服务工作者 API、Fetch API、IndexedDB 等等。他们可以用](https://developer.mozilla.org/en-US/docs/Web/API) [WebIDL](https://heycam.github.io/webidl/) 来描述。

### 不那么标准

标准听起来不错，但问题是每个浏览器的实现略有不同。有些根本没有实现某些 API(因为他们没有时间或者不想实现),或者他们只实现了 API 的一部分，或者实现中有 bug。因此，当你为 web 开发时，你不能只在一个浏览器上写代码并测试它，你需要在所有浏览器上测试它。这是一个长期存在的问题，人们反复试图解决它，例如:

*   开发人员仅在一种浏览器中进行了测试，并添加了免责声明。该网站最好在 internet explorer 中查看，或稍后在 google chrome 中查看
*   相同的浏览器(由相同的供应商提供)在不同的机器上不能以相同的方式工作，所以[人们引入了带有浏览器的虚拟机来测试](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/)并且出现了像[浏览器堆栈](https://www.browserstack.com/)这样的工具
*   人们编写了一些解决浏览器差异和错误的库，其中一个最大的例子是 jQuery，但是像 React 这样的现代库仍然这样做
*   人们有了 polyfill 的想法，使浏览器行为更加一致，后来又创造了 [polyfill.io](https://polyfill.io/)
*   还有一个 [autoprefixer](https://github.com/postcss/autoprefixer) ，它将为 CSS 属性添加特定于浏览器的前缀
*   有 [babeljs](https://babeljs.io/) 可以将 ES6 或更高版本移植到 ES5(这得到了广泛支持)

所以，是的，这是一个非常大的问题，很多人致力于这个问题，花了很多时间，围绕这个问题建立了企业。

### Caniuse

[can use](https://caniuse.com/)是一个众包数据库，包含哪些浏览器(供应商、版本)支持哪些 ECMAScript 和/或 WebAPI。它可以作为一个在线网站或独立的数据库。

### 浏览器列表

Browserslist 是一个 DSL，在其中你可以指定你想要支持哪种浏览器。哪些供应商，下到哪个版本等等。

### 网络捆扎机

那里使用最多的捆扎机是 [WebPack](https://webpack.js.org/) ，还有[包裹](https://parceljs.org/)和[西兰花](https://github.com/broccolijs/broccoli)和 [browserify](http://browserify.org/) 。

## 愿望 1:无痛聚脂

1.  决定我们要支持哪些浏览器，并用 Browserslist 声明。有`defaults`所以你不必选择如果你不想
2.  编写一些代码，就好像所有最新的特性都可用一样
3.  编译器分析代码并决定您的代码需要哪些聚合填充(但只需要稳定的标准)
4.  默认情况下，编译器使用最新的稳定特性，如`babel-preset-latest`

编译器会考虑你的代码需要什么(3)和你的目标是什么(1 ),并创建具有相同特性的浏览器。因此，将会有支持所有(或几乎所有)功能的浏览器，并且代码可以不经过编译而交付，例如，具有动态导入和提取非填充的 ES6。多酷啊。

Transpiler 也需要分析所有的依赖项，因为它们可能需要额外的 polyfills，这意味着依赖项应该作为 ES6 模块分发(非 polyfills)。有些库是用不同的语言实现的，比如 TypeScript，它们需要被编译成 ES6 模块。同时，这意味着我们将需要 ES6 迷你。

除了特定于浏览器的包之外，编译器还将为资产路由器生成配置，例如，服务器、Cloudflare-Worker 或 AWS Lambda@Edge 将使用某些配置来决定将哪些资产提供给哪个浏览器。

“分桶”的一个潜在问题是，我们最终可能会有大量的桶，因此应该有一种方法来限制桶的数量，并使用一些启发式方法来决定最佳方案。

灵感:

*   [在所有浏览器中使用 JavaScript 模块，包括动态导入](https://github.com/Rich-Harris/shimport)
*   [每个供应商捆绑包的手册示例](https://github.com/kristoferbaxter/preact-hn/tree/master/config)

## 愿望 2:可安装组件

这又是一个长期存在的问题，很多人试图解决它，我们就快成功了。

它始于 DHTML 和 jQueryUI 时代，当时人们开始用 JS 构建丰富的 UI。有组件，像日期选择器或组合框。要安装一个，你需要下载一个包含所有文件的 zip 文件，比如 JS，CSS，图片等等。您需要确保依赖关系的版本手动匹配，您需要手动更改资产的路径，您需要以某种方式处理缓存，并且您不更新它。里程可能会有所不同，但我猜这是非常接近整体体验

人们试图用以下方法解决这个问题:

*   CommonJS、AMD、UMD、ES6 模块和 npm、Bower——能够分发组件包
*   Grunt，Gulp -作为资产管道，配置相对路径，缩小和创建唯一的名称(用于缓存)
*   Browserify、WebPack、Parcel - as 资产管道，还具有`import(asset)`和理解不同模块格式的能力，如 CommonJS 和 ES6

我们快到了。我们能

*   从 npm 安装软件包
*   只需通过`import from`或`import()`即可使用
*   对于 React(或 Vue 等)组件，使用 CSS-in-JS，我们无需担心包含 CSS 文件
*   如果是 React(或 Vue 等)组件，我们可以将内嵌 SVG(作为组件)用于图标和图像
*   ❌but 我们仍然不能通过 npm 分发带有资产依赖的包，比如图像或字体，因为使用资产导入是无效的 JS

这是一个愿望清单，所以我没有解决方案。我只是想安装软件包，需要组件，它应该工作，不需要配置资产的路径，不需要配置缩小，资产的唯一名称，不需要配置 bundler 或 transpiler 等。

CSS-in-JS 和内联 SVG 组件使我们更接近目标，但 CSS-in-JS 也有它的缺点，如没有 JS 它就不能工作(有一些项目试图解决它),而且它“供应商锁定”了一些 JS 特定的实现。CSS-in-JS 没有单一的解决方案，所以我们最终可以在一个项目中使用多个 CSS-in-JS 解决方案。

另一方面，我们在 CSS-in-JS 和纯 CSS - CSS 模块之间有一个相当不错的中间地带。要在 npm 包中使用它，我们需要允许捆绑器特定的“资产导入”，但我们已经允许一些捆绑器特定的代码，如，`process.env.NODE_ENV === "development"`。我们还需要区分 CSS 模块和普通 CSS——为此，我们有一个既定的惯例，使用`.module.css`作为 CSS 模块。所以也许我们能成功。

## 愿望 3:快速反馈循环

人们在这方面工作了很长时间，包括:

*   为了更快的编译，流一饮而尽
*   浏览器同步，这样你的网站会在你保存文件后立即重新加载
*   [Webpack 的热模块更换](https://webpack.js.org/concepts/hot-module-replacement/)
*   [React Hot Reload](https://github.com/gaearon/react-hot-loader) 和 Redux 在重载和后续 React 挂钩中保持状态

当前的实现已经很不错了，但是还有 TypeScript，可能会很慢。解决方案是将编译和类型检查分开，例如，用 Babeljs 和在单独的进程运行类型检查器中编译。

Babel 和 Webpack 和 Parcel 都很不错，但是没有 OCaml 好。不知道能不能做的更好？..一些高性能的 Rust 代码被编译到 WASM🤔

灵感:

*   Tree sitter:一个用于编程工具的增量解析系统
*   Pax:银河系中最快的 JavaScript 捆绑器

## 愿望 4:零配置

当人们谈论零配置时，他们指的是:

*   约定胜于配置，工具的作者花时间来决定什么工作最好，什么是安全的赌注。一个很好的例子是，Create-React-App 它可以保护您免受攻击，它提供了所有最需要的聚合填充，它支持许多开箱即用的东西
*   工具之间的无缝集成，工具的作者花时间来确保它们一起工作，而无需您担心。一个很好的例子是包裹，你只需使用你需要的包裹，它们就能工作

零配置允许消除工具附带的复杂性。您可以立即开始编码，无需担心配置问题。
零配置允许轻松升级工具。

我们有一些零配置的优秀例子，但是我仍然不得不面对大量的配置或者担心工具之间的集成。

*Louie Martinez 在 Unsplash 上拍摄的照片*