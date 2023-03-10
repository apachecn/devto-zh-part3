# 在 Ruby on Rails 上使用 Webpack

> 原文：<https://dev.to/justalever/using-webpack-with-ruby-on-rails-1n37>

尽管 Webpack 配置起来有点困难，但它仍然是现代 web 开发的前沿构建工具。这篇文章/视频指导你将 Webpack 添加到一个旧的 Ruby on Rails 5.2 项目以及最新的 Ruby on Rails 6 beta 版本中。

这里的演示是对无论版本如何，如何在 Ruby on Rails 中使用 Webpack 的高层次概述。

我们将利用 [webpacker gem](https://github.com/rails/webpacker) (默认情况下现在是 Rails 6 的一部分)将更现代的 JavaScript 合并到一个已经成熟的框架中。添加 webpack 的目的是添加新技术，这些新技术太新了，需要编译成更多的遗留代码。做到这一点并不容易，但是 gem 将会把所有的硬配置从图片中去掉，并最终正常工作。

使用 Webpacker，您可以获得这些功能以及更多功能:

*   webpack 4.x.x
*   带巴别塔的 ES6
*   使用多个入口点的自动代码拆分
*   样式表–Sass 和 CSS
*   图像和字体
*   PostCSS – Auto-Prefixer
*   资产压缩、源地图和缩小
*   CDN 支持
*   React、Angular、Elm 和 Vue 支持开箱即用
*   Rails 视图助手

Rails 已经在资产管道中处理了很多这样的事情。你可以选择使用图片、字体、css 等资源管道，依赖于 JavaScript 的 webpacker。这将是 Rails 6 的新默认设置。

[https://www.youtube.com/embed/yhO3PCxP5hs](https://www.youtube.com/embed/yhO3PCxP5hs)

## 常见问题

### 如果资产管道已经存在，为什么我还需要带有 Rails 的 webpack？

实际上，在大多数情况下，你*不会*，但它确实可以用最少的设置时间更容易地访问像 Vue.js 或 React.js 这样的现代前端框架。资产管道仍然像它应该的那样与新的 webpack 工作流一起工作。与 webpack 相关的所有资产现在都位于一个`app/javascript`目录中。

### web pack 是否依赖于资产管道？它们有冲突吗？

webpacker gem 并不直接依赖于资产管道，尽管它确实与每个 Rails 安装捆绑在一起的几个 rake 命令挂钩，例如`rails assets:precompile`将启动资产管道编译和 webpack 命令，以便利用这两者来编译给定项目中的所有资产。只有当你的代码发生冲突时，它们才会发生冲突。将关注点分开是一个很好的做法。我倾向于在`app/javascript`中创作所有的 javascript，然后为任何`css`、图像或我正在进行的项目可能需要的其他资产寻找资产管道`app/assets/`。

### 我还需要添加 webpack 的资产管道吗？

实际上，没有，但是资产管道有许多方便的特性，可以在您或您的团队可能已经习惯的视图中呈现资产。幸运的是，您可以对`app/javascript`中的资产做同样的事情。渲染图像、资产、CSS、JavaScript 等等是非常可能的。

### 无耻的塞时间

如果你喜欢这篇文章，我在 [YouTube](https://youtube.com/c/webcrunch) 和我的[博客](https://web-crunch.com)上有更多视频。希望收件箱中有更多这样的内容吗？[订阅我的简讯](https://web-crunch.com/subcribe)自动获取。

#### 查看我的课程

[![https://i2.wp.com/i.imgur.com/KIaaJEB.jpg?ssl=1](img/083250001f26c8017cdaafe107a730f9.png)](https://hellorails.io)

想从头开始学习 Ruby on Rails 吗？查看我即将推出的课程 [Hello Rails](https://hellorails.io) 。

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。

帖子[使用 Webpack 和 Ruby on Rails](https://web-crunch.com/using-webpack-with-ruby-on-rails/) 首先出现在[的 Web-Crunch](https://web-crunch.com) 上。