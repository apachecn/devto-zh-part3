# 使用 Vue 创建一个没有任何节点模块的 SPA

> 原文：<https://dev.to/arswaw/create-a-lightweight-componentized-spa-without-node-569j>

在本指南中，我将向您展示如何使用 VueJS 和 ES6 模块创建一个静态的单页面应用程序。

我的一个朋友试图使用 AWS Cloud9 来构建他的 SPA，但他发现由于 Cloud9 的工作方式，使用 Node 很麻烦。我设计了一种方法来构建一个复杂的 web 应用程序，在工作时完全不使用 Node。我把知识传授给他，他也接受了。

这个想法是，如果你愿意，你可以稍后用可选的**构建工具构建一个“静态优先”的 web 应用程序。**

不要使用 Node 的模块系统，你可以使用内置在 JavaScript 中的[模块。该模块系统在版本 6 中提供。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)

这允许您将文件原样发送到浏览器。没有设置。您可以将 git 存储库直接克隆到 Apache 上的/var/www/html，然后开始开发。

你可以使用普通的 JavaScript，但我发现 Vue.js 承担了我在一个中等复杂的应用程序中无论如何都要做的繁重工作。

您将需要一个服务器来托管您的静态文件。我推荐 [fenix。](http://fenixwebserver.com)

你需要使用支持 ES6 的浏览器，比如 Opera。

你可以在这里看到最终代码[。](https://github.com/arswaw/VueSpaNONODE)

我在使用这种方法时遇到了 Edge 的问题，但 Rollup.js 在准备应用程序跨浏览器工作时非常有用。

我正在构建一个 CLI 来自动化这些步骤。

#### 步骤 1 -创建您的 index.html 文件。

在很大程度上，您的 index.html 文件将保持不变，因为您的大部分工作将在 JavaScript 中完成。

1.  用您选择的名称创建一个项目文件夹。从您希望的任何静态文件服务器提供服务。
2.  在项目文件夹中，创建一个 index.html 文件。
3.  添加 html、head 和 body 标记，然后在 body 标记之间包含以下代码行:

```
<script type="module" src="main.js"> 
```

Enter fullscreen mode Exit fullscreen mode

如果在控制台中遇到 499 错误，可以选择包含“crossorigin”。本论坛[帖子有更多信息。](https://forums.aws.amazon.com/thread.jspa?messageID=878957&tstart=0)

1.  在您刚刚编写的代码行上方，添加以下内容:

```
<div id="app"></div> 
```

Enter fullscreen mode Exit fullscreen mode

您可以随意调用该 id，只要它与我们将要创建的 Vue 实例相匹配。

#### 第二步——App 入口点，与 Vue 整合。

Vue 可以作为 ES6 模块导入。所有组件都可以使用它。

你应该在一个目录中找到一个文件，就是你刚刚创建的 index.html 文件。

1.  创建一个 main.js 文件。
2.  创建一个 vue.js 文件。
3.  转到此 [cdn](https://cdn.jsdelivr.net/npm/vue/dist/) 并选择 vue.esm.browser.js
4.  将 JavaScript 代码复制并粘贴到您刚刚在项目中创建的 vue.js 文件中。
5.  转到 main.js 并输入以下代码:

```
import Vue from './vue.js' 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以这样做:

```
import Vue from 'https://cdn.jsdelivr.net/npm/vue@latest/dist/vue.esm.browser.min.js' 
```

Enter fullscreen mode Exit fullscreen mode

感谢 Github 上的 [Mohamed Safouan Besrour](https://github.com/BesrourMS) 的建议和请求！

1.  在这一行之后，立即添加 Vue 实例:

```
new Vue({
  el: '#app', // This should be the same as your <div id=""> from earlier.
  components: {
    'navbar': Navbar
  },
  template: MainTemplate
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们引用了两个外部文件、一个组件和一个模板。我们将同时进口这两种产品。“navbar”组件有自己的模板，它将自己导入模板，而不必导入 main.js

这很重要，因为我们想在组件化的应用程序中实践关注点分离。

#### 第三步-添加模板。

对于我用这种方法构建的应用程序，我将所有模板保存在一个单独的文件夹结构中，该结构反映了组件文件夹结构。如果你想的话，你可以改变一下。没有理由为什么你不能用模板给每个组件一个自己的文件夹。js 文件。这类似于 Angular 采取的方法。

1.  创建模板目录。这应该是应用程序目录中唯一的子文件夹。
2.  创建一个名为 main-template.js 的文件
3.  添加以下代码:

```
// Note that these are backticks.

const MainTemplate = `
    <div>
    <navbar></navbar>
    All content will appear below the horizontal line.
    <hr>
    </div>
`

export { MainTemplate } 
```

Enter fullscreen mode Exit fullscreen mode

1.  在 templates 文件夹中，创建一个 navbar-template.js 文件。
2.  添加以下代码:

```
const NavbarTemplate = `<nav>

<a href="#/">Home</a>
<a href="#/about">About</a>

</nav>`

export { NavbarTemplate } 
```

Enter fullscreen mode Exit fullscreen mode

我们已经完成了模板的创建。

#### 第四步-添加组件。

我们需要一个导航栏组件来附加我们的模板。

1.  在项目的根目录下，创建一个“组件”目录。
2.  创建一个 navbar.js 文件。
3.  添加以下代码:

```
import { NavbarTemplate } from '../templates/navbar-template.js'

const Navbar = {
  template: NavbarTemplate,
}

export { Navbar } 
```

Enter fullscreen mode Exit fullscreen mode

1.  返回 main.js

#### 步骤 5 -导入我们的组件。

我们几乎已经准备好查看 web 应用程序的第一个版本了。

1.  在 main.js 中，在 Vue 导入和 Vue 实例化之间添加以下代码:

```
import {
  Navbar
} from './components/navbar.js'

import {
  MainTemplate
} from './templates/main-template.js' 
```

Enter fullscreen mode Exit fullscreen mode

1.  保存文件并在浏览器中查看您的 web 应用程序。该 URL 将取决于您的服务器设置。

我们有一个 web 应用程序，可以选择变成一个水疗中心。我们现在就开始。

#### 第六步-添加路由。

我们需要进口 Vue 路由器。这是可选的，但是当涉及到路由时，它又为您做了许多繁重的工作。

1.  导航到 vue 路由器 [CDN。](https://cdnjs.com/libraries/vue-router)似乎没有 ES6 模块兼容版本，所以我们将作为 CDN 导入。
2.  复制链接，并将其作为依赖项添加到模块导入上方的 index.html 中:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue-router/3.0.2/vue-router.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

1.  返回 main.js 并添加以下代码:

```
Vue.use(VueRouter)

const router = new VueRouter({
  routes: [{
    path: '/about',
    component: About,
    name: "About Us Page"
  }]
}) 
```

Enter fullscreen mode Exit fullscreen mode

1.  在 Vue 实例中，在主模板上方添加“路由器”:

```
router,
template: MainTemplate 
```

Enter fullscreen mode Exit fullscreen mode

1.  创建一个 about 模板，一个 about 组件，然后将组件导入 main.js
2.  在 main-template.js 中的 hr 标记下，添加:

```
<router-view></router-view> 
```

Enter fullscreen mode Exit fullscreen mode

1.  查看您的应用程序主页，然后使用导航栏转到“关于”页面。

添加新组件和模板来扩展您的应用。

检查控制台。如果您看到任何错误，或者应用程序没有按照您预期的方式呈现，请检查您是否正确执行了这些步骤。如果还是不行，就在帖子下面留言评论。

这种方法的主要观点是，为了开发一个 SPA，您不一定需要用 Node 设置一个复杂的构建环境。我相信，随着模块开发者发布他们软件的 ES6 兼容版本，这种类型的开发将变得更加可持续。

未来的一篇博客文章将重点介绍如何使用 Rollup.js 来打包这种类型的应用程序，并设置 CI/CD。

我发现了 V2 编辑器的另一个漏洞。它会导致编号在代码块后重置。注意我的数字现在是 1。每个代码块关闭后。我会在 Twitter 上联系 DEV，讨论这个 bug。

我希望这能帮助那些希望只用 HTML、CSS 和 JavaScript 构建复杂 web 应用程序的人。

谢谢你。