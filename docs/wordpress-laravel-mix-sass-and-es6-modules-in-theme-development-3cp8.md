# 主题开发中的 Laravel Mix，Sass 和 ES6 模块

> 原文：<https://dev.to/vanaf1979/wordpress-laravel-mix-sass-and-es6-modules-in-theme-development-3cp8>

[最初发布于 2019 年 3 月 19 日](https://since1979.dev/wordpress-laravel-mix-sass-and-es6-modules-in-theme-development/)

### 现代(WordPress)发展:

在这篇教程/文章中，我将向你展示一个简单的方法来开始使用 Sass 和 ES6 模块来使你的 WordPress 主题开发工作流程现代化。Sass 和 ES6 模块都可以使您的开发变得更加容易，帮助您更好地组织代码，并最终使您的代码更易于维护。有了这个设置，您还可以开始使用新的 javascript 特性，比如箭头函数。

在本文的最后一部分，我将给出一些使用 Sass 和 ES6 模块的例子，向您展示使用这些技术的好处。

现在，在我们开始使用 Sass 和 ES6 模块之前，我们需要安装一个叫做 assats bundler 的东西。现在你可能听说过 [Webpack](https://webpack.js.org/) 了！？Webpack 可能是最好的 assats bundler，但是它提供了太多的选项，对于大多数开发人员来说很难配置。幸运的是，这个问题有一个解决办法。

[杰弗里·韦](https://twitter.com/jeffrey_way)的 Laravel Mix “提供了一个干净、流畅的 API，用于定义应用程序的基本 [webpack](http://github.com/webpack/webpack) 构建步骤”。Laravel Mix 最初是为了在 Laravel 应用程序中使用而创建的(因此得名)，但它也可以很容易地用于其他项目，如 WordPress 主题和插件。

要将 Laravel Mix 安装到您的项目中，您将需要 NPM。NPM 是一个 CLI 软件包经理。您可以使用它将各种包(Javascript 库、Css 框架等等)快速安装到您的项目中。

好吧，这听起来像是很大的工作量。但是相信我，这并不像听起来那么难，我将向你展示最简单的方法。

作为这篇文章的参考，为了帮助你理解这篇文章，我已经设置了一个基本的 WordPress 主题，它是我将要解释的内容的工作设置。你可以从[这个 Github 库](https://github.com/vanaf1979/wordpress-and-laravel-mix-tutorial)下载。

[![](img/539d76f812fec9c10a15f516993f3149.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iltCfOWX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AkvBbMb9_R-uuebS4IChhmQ.jpeg)

### NPM 在 WordPress 主题开发:

NPM 或节点包管理器是一个广泛使用的命令行工具，用于安装 Javascript 和 Css 包。我们将安装它，这样我们就可以开始使用 Laravel Mix，除此之外，还可以安装我们可能需要的 javascript 库。

### 安装 Npm:

NPM 挤满了 NodeJs。要开始，只需下载并安装最新版本的 NodeJs 到你的机器上，这将自动安装 NPM。(在一些罕见的情况下，我注意到需要重启你的机器)。

要检查 NodeJs 安装是否正确，请打开您的终端并运行以下命令:

```
$  node -v 
```

Enter fullscreen mode Exit fullscreen mode

这应该会输出 node.js 版本号

要检查 NPM 安装是否正确，运行以下命令:

```
$  npm -v 
```

Enter fullscreen mode Exit fullscreen mode

这将输出 NPM 版本号。

### 为你的项目/主题初始化 NPM:

要为你的项目初始化 NPM，你可以[在你的主题文件夹中运行 *$ npm init* 命令](http://weaintplastic.github.io/web-development-field-guide/Development/Frontend_Development/Setting_up_your_project/Setup_Dependency_Managers/Node_Package_Manager/Initialize_NPM_on_a_new_project.html)并回答一系列问题，最后用你输入的信息创建一个名为 *package.json* 的文件。但是根据我在 WordPress 主题中使用 NPM 的经验，你可以很容易地自己创建这个文件。所以让我们选择这个选项。

在你的主题文件夹的根目录下创建一个名为 *package.json* 的文件，将下面的【json 代码粘贴进去: