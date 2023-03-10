# 如何检查生成的样板文件的选项？

> 原文：<https://dev.to/peterbabic/how-to-examine-the-options-of-a-generated-boilerplate-5648>

这篇文章是多个领域的混合体，主要是 Nuxt 项目搭建和我经历的挣扎，以及一些 shell 脚本，当这整个旅程让我更深入的时候。如果你对这些感兴趣，欢迎你继续阅读。

# Nuxt 项目生成

*Nuxt.js 是一个用于创建 Vue.js 应用程序的框架，你可以在通用、静态生成或单页面应用程序之间进行选择。*T3】

按照他们的[安装页面](https://nuxtjs.org/guide/installation#using-code-create-nuxt-app-code-)上的说明，使用`create-nuxt-app`搭建工具，第一步是*在集成服务器端框架*之间进行选择，目前在 Nuxt 版本`2.6.3`中，你可以选择以下之一:

*   无(Nuxt 默认服务器)
*   表达
*   寇阿相思树
*   哈皮
*   羽毛
*   微观的，具体领域的
*   阿多尼斯(WIP)

如果你和我一样是一个普通的节点用户，很有可能你以前用过 Express。甚至可能会有“Hello World！”利用它作为散布在互联网上的节点的快速入门指南的例子，所以不用太多思考，选择 Express 似乎是一个显而易见的选择。

安装的其余部分提供了多种令人惊叹的选项来帮助您继续工作。我真的很高兴看到 [Buefy](https://buefy.org/) 和 [Jest](https://jestjs.io/) 在名单中。希望你也能在那里找到你最喜欢的选择。

项目生成后，您就可以开始构建了。您编写了第一个 Vue 组件，并希望将数据放入其中。Axios 模块基本上就是要走的路，Nuxt 在它的基础上增加了一些功能。您可以在 it 的[文档](https://axios.nuxtjs.org/)中了解更多信息。

# 获取数据

但是 Axios 从哪里获取数据呢？当然，从第一步选择的集成服务器端框架开始！我们选择了 Express，它必须能够轻松地为我们简单的应用程序提供数据。没问题——有一个文档化的方法:使用 [serverMiddleware](https://nuxtjs.org/api/configuration-servermiddleware/) 属性。

不幸的是，它并不完美。有一个开放的[问题](https://github.com/nuxt/nuxt.js/issues/4301)与两个相关的关闭问题。长话短说，您可以使用`serverMiddleware`通过选择的框架(来自 Nuxt 安装的步骤 1)来提供数据，但是开发仍然需要大量的服务器重启，这很痛苦。您基本上需要有两个独立的部分——服务器和 Nuxt 项目。当然，当您使用微服务时，这是意料之中的，但是对于最简单的项目，我不建议直接从 Nuxt 应用程序本身提供数据(尽管如果您关注这个问题，您可以看到它正在进行中)。

选择 Express 对我们的服务器开发没有太大的帮助。这不是因为 Express 本身的问题，而是因为 HMR(热模块更换)和缓存的未解决问题。这使得选择所有其他(koa，hapi 等。)同样无益。列表中只有一个不言自明的选择:第一个。

# Nuxt 默认服务器

`None (Nuxt default server)`到底是什么意思？我在任何地方都找不到关于这个选择的更多细节——也许我需要提高我的搜索技巧。然而，我决定重拾我的 shell 脚本技能。

通过生成两个相同的 Nuxt 项目，一个名为`default-server`，另一个名为`express-server`，选择指定的服务器端框架选项作为唯一的区别。准备比较项目以了解更多信息时，我发出了以下命令:

```
 diff -r default-server express-server 
```

Enter fullscreen mode Exit fullscreen mode

如果你提前考虑，你就能理解为什么这不是一个好主意。我的屏幕立刻充满了无用的数据。于是我学会了如何从比较中排除一些东西，并且更进一步:

```
 diff -x node_modules -r default-server express-server 
```

Enter fullscreen mode Exit fullscreen mode

这很有帮助，但是仍然包含了许多不相关的数据。我已经应用了更多的排除:

```
 diff -x node_modules -x package-lock.json -x .nuxt -x README.md -r default-server express-server 
```

Enter fullscreen mode Exit fullscreen mode

真的很有帮助但是有一点可以改进:

```
 diff -x node_modules -x package-lock.json -x .nuxt -x README.md -r default-server express-server 
--color 
```

Enter fullscreen mode Exit fullscreen mode

完美。

然而，结果比我想象的要小得多。唯一值得一提的区别是服务器是如何启动的，以及单个文件在`express-server/server/index.js`中的存在。自己看:

```
nuxt.config.js
1c1 - import pkg from './package'
-------- + const pkg = require('./package') 3c3,4 - export default {
-------- + module.exports = { 
package.json
2c2 -   "name": "default-server",
-------- +   "name": "express-server", 8c8 -     "dev": "nuxt",
-------- +     "dev": "cross-env NODE_ENV=development nodemon server/index.js --watch server", 10c10 -     "start": "nuxt start",
-------- +     "start": "cross-env NODE_ENV=production node server/index.js", 15c15,16 -     "nuxt": "^2.4.0"
-------- +     "nuxt": "^2.4.0",
+     "express": "^4.16.4" 
pages/index.vue
6c6 -         default-server
-------- +         express-server 
Only in express-server: server 
```

Enter fullscreen mode Exit fullscreen mode

遗憾的是，深入探讨超出了今天文章的范围。

# 结论

Nuxt 搭建工具让你选择集成的服务器端框架。不仅可以用它来为项目服务(捆绑的 JS、CSS 和 HTML ),也可以用它来为实际数据服务。然而，这是相当不切实际的，因为它需要不断地重新启动服务器来应用哪怕是最轻微的更改，但这在将来可能会改变。

对于最简单的项目，不选择使用选项`Nuxt default server`的服务器端框架看起来是比 Express 更好的选择，因为少了一个依赖。无论如何，您可能不会太多地利用 Express(或任何其他服务器端框架)。

你可以在这个[要点](https://gist.github.com/peterbabic/7340cdaed731101d8b809c28ace84329)中看到这两个脚手架项目的完全不同。我觉得我错过了一些非常明显的东西，所以请帮我指出重要的部分。干杯！