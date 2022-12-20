# 为 react 项目设置 webpack 并不可怕

> 原文：<https://dev.to/mzubairahmed/setting-up-webpack-for-react-project-doesnt-have-to-be-scary-27k6>

如果你在几周前要求我为新的 react 项目建立一个完整的构建和锅炉系统，我会给你一个 [C.R.A](https://github.com/facebook/create-react-app) 。
所以，如果你的反应是一样的，这篇博文是给你的。

所有的标题都指向 git commits，所以您可以在 Github 中浏览标题/步骤状态的项目。

## 准备新项目

### [添加包 json](https://github.com/M-ZubairAhmed/react-scaffolding-kit/tree/9056c5bfa7e699ae6cba6c5325ca36d9934c737f)

从一个空文件夹开始，运行`yarn init`。这将问你一堆问题，并为你制作一个文件，如`package.json`。您可以填写您的项目名称和其他详细信息。

### [安装 react 库](https://github.com/M-ZubairAhmed/react-scaffolding-kit/tree/92c95d6ee96e9991e8e56cb27959be6ae4530a92)

安装 React 及其 dom 库

```
yarn add react react-dom 
```

### [安装 webpack](https://github.com/M-ZubairAhmed/react-scaffolding-kit/commit/29e5cdb7463dc404f74506918f72cf6cb3302427)

将核心 [webpack](https://github.com/webpack/webpack) 包安装为*devDependencies，同时添加另外两个 webpack 包； [webpack cli](https://github.com/webpack/webpack-cli) ，如果我们要用命令行或通过 npm 脚本和 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) 运行 webpack，这将是必需的，这个软件包让我们可以启动并运行我们的本地开发环境，而无需太麻烦地制作我们自己的服务器。

```
yarn add --dev webpack webpack-cli webpack-dev-server 
```

** devDependencies:是那些你不希望包含在应用程序中，但只有在构建或开发它们时才包含的包。*

### [装漂亮点](https://github.com/M-ZubairAhmed/react-scaffolding-kit/commit/7d0474cba6fda4a579b2e6ff0d5dc6e07d50b506)

[更漂亮](https://prettier.io/)提供漂亮的代码格式。由于这与生产代码无关，我们只打算在开发代码时使用它，所以也将它安装为 devDependency。

```
yarn add prettier --dev --exact 
```

有许多配置可以满足您的偏好。前往[文档](https://prettier.io/docs/en/options.html)进行配置。在根目录下创建一个名为`prettier.config.js`的新文件，并将这两个配置添加到其中。稍后您可以随意添加自己的内容。

```
module.exports = {
  trailingComma: 'all',
  semi: false,
} 
```

### [写一些 react 代码](https://github.com/M-ZubairAhmed/react-scaffolding-kit/commit/491dd4a9f5168232c2afe4c988051d9c49d35dee)

在根目录下创建一个名为`src`的文件夹，并将文件`index.js`添加到其中。
Index.js 将是 src 的起点。目前这个文件很简单，以后还可以扩展。跟随[这里的](https://reactjs.org/docs/hello-world.html)学习如何编写 react 代码。

```
import React from 'react'
import { render } from 'react-dom'

const App = () => (
  <div>
    <h1>React scaffolding kit</h1>
    <strong>An opiniated but customizable react starter kit</strong>
  </div> )

render(<App />, document.getElementById('REACT_ROOT')) 
```

### [运行 app](https://github.com/M-ZubairAhmed/react-scaffolding-kit/commit/ea1684f0005f076ef85601582d6b8968dcfbb818)

让我们利用之前安装的`webpack-dev-server`来运行这个应用程序。在 package.json 中有一个启动脚本是一个好主意，可以方便地启动。在`package.json`中添加启动脚本。

```
"scripts":  {  "start":"node_modules/.bin/webpack-dev-server",  }, 
```

现在，应用程序可以通过终端中的一个简单命令启动

```
yarn start 
```

您可能会看到与 webpack 相关的内容出现在您的终端中，并最终以一个错误结束。如果错误出现在**行，你可能需要一个合适的加载器来处理这个文件类型**或相关的。那么不要担心你已经正确地遵循指示。让我们在下一节解决这个错误。去喝杯水。
顺便提一下，你可能已经注意到 webpack 能够识别你在 *src/index.js* 中编写的代码，而无需你为它提供任何配置。这是因为 webpack 现在提供了很多默认设置，可以让你很快上手。因此将`index.js`文件放在`src`下是 webpack 的默认入口点[。](https://webpack.js.org/concepts#entry)

### 传输 Javascript 代码

我们犯的错误意味着我们的服务器无法理解我们写的代码。它与通常浏览器理解的内容不兼容。为了能让它理解，我们用 [Babeljs](https://babeljs.io) 把我们的代码移植到更好的浏览器理解代码。

### [安装 babeljs](https://github.com/M-ZubairAhmed/react-scaffolding-kit/commit/842e57e18cbdfa2bd2c5b5d129049919497b7c95)

```
yarn add --dev @babel/core 
```

Babel core 安装 Babel 库的核心功能。为了让 webpack 意识到巴别塔，说嗨！我想巴贝尔 transpile 我的代码之前，你可以做你的东西，我们将添加另一个包

```
yarn add --dev babel-loader 
```

### [连接巴别塔和网络包](https://github.com/M-ZubairAhmed/react-scaffolding-kit/commit/f1ece0ce227b351fff4892955e3bb46574a1d4a0)

通过添加软件包，webpack 仍然不知道巴别塔。为此，我们为 webpack 添加一个配置文件，并将其命名为`webpack.dev.config.js`。为什么名字里有 dev？这是因为我们希望将我们的生产和开发 webpack 配置分开。

```
module.exports = {
  module: {
    rules: [{ test: /\.js$/, exclude: /node_modules/, use: 'babel-loader' }],
  },
} 
```

综上所述，我们想首先在所有的 **js** 文件上运行 [babel-loader](https://github.com/babel/babel-loader) ，除了那些在`node_modules`中的文件

### [配置通天塔](https://github.com/M-ZubairAhmed/react-scaffolding-kit/commit/c2ce7526e0bed1a3b72081009a66ec8747292f0b)

好了，现在你已经配置了 Babel，但是你还没有让它做任何事情。让我们在根目录下创建`babel.config.js`。Babel 依靠许多插件来完成它的工作。这些插件包含了如何将代码解析成更兼容的代码的说明。让我们添加一些我们需要的插件。首先，添加[包络预设](https://babeljs.io/docs/en/babel-preset-env)，它为 ES2015+和[反应预设](https://babeljs.io/docs/en/babel-preset-react)启用变换，后者包含与反应一起使用的最常用插件。你可以自由添加[包括其他](https://babeljs.io/docs/en/plugins)。预设只是一堆组合在一起的插件。

```
module.exports = function(api) {
  if (api) {
    //   https://babeljs.io/docs/en/config-files#apicache
    api.cache.using(() => process.env.NODE_ENV)
  }

  const presets = ['@babel/preset-env', '@babel/preset-react']

  const plugins = []

  return {
    presets,
    plugins,
  }
} 
```

### [再次启动 app](https://github.com/M-ZubairAhmed/react-scaffolding-kit/commit/6bd27dae7a1c1fa5e3e55e378f2d3500e6753f72)

让我们运行之前运行的 start 命令。在开始之前，我们需要做一个小小的修改，告诉 *webpack 开发服务器*从我们之前创建的新`webpack.dev.config.js`文件中读取配置。

```
"start":  "node_modules/.bin/webpack-dev-server --config webpack.dev.config.js", 
```

现在你一定看到当我们启动服务器时，我们的终端没有抛出任何错误。您现在可以转到这里提到的本地主机。如果你在你的网站上只看到一堆文件夹，不要失望，我们还没有配置应用程序输出 **HTML** ，所以它只是向我们显示网站的内容。

### [让 app 吐槽 html](https://github.com/M-ZubairAhmed/react-scaffolding-kit/commit/7612a5cb99635fb0d87b983304ac3a1264893269)

Webpack 包含插件和加载器的生态系统。为了使 webpack 能够输出 html，我们将添加一个 HTML webpack 插件。

```
yarn add --dev html-webpack-plugin 
```

创建一个 html 文件，它将由我们的应用程序在 *src* 文件夹中提供，并只添加简单的 HTML5 支持内容。还记得[我们让](#write-some-react-code) react 寻找元素 id `REACT_ROOT`吗，包括它以便 react 应用程序在元素树上渲染。

```
<!DOCTYPE html>
<html>
  <head>
    React scaffolding kit
  </head>

  <body>
    <div id="REACT_ROOT"></div>
  </body>
</html> 
```

现在我们需要配置 webpack，并要求它将我们的 html 作为模板输出。所有 webpack 配置都在 webpack 配置文件中完成。为 webpack 对象添加一个新的键`plugins`,并导入我们新添加的插件。插件现在可以添加到插件数组中了。我们将通过提供`template`选项要求它使用我们刚刚创建的 html。

```
const htmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  module: {
    rules: [{ test: /\.js$/, exclude: /node_modules/, use: 'babel-loader' }],
  },
  plugins: [
    new htmlWebpackPlugin({
      template: './src/index.html',
    }),
  ],
} 
```

一旦完成，你可以从 *start* 脚本重启应用程序。现在，应用程序应该会用写着大大的 H1 标签的**反应脚手架工具包**来迎接你。

### [添加造型](https://github.com/M-ZubairAhmed/react-scaffolding-kit/commit/9b02e51d8b35b554c3e2f3407a48ea8af5ee12e2)

该应用程序看起来相当平淡，现在让我们添加添加样式的能力。我将添加 **SCSS** 的支持，因为我觉得这是写 CSS 的最好方式。在 src 目录下创建一个新文件，命名为`index.scss`。

```
h1 {
  color: blue;
} 
```

通过导入将样式包含在您的`index.js`中。

```
import './index.scss' 
```

但是正如你已经注意到的，应用程序似乎没有选择样式。原因是 webpack 本身只能做这么多。我们必须为 webpack 提供一种在应用程序中包含样式的方法，现在 webpack 加载程序来拯救我们了。

```
yarn add --dev style-loader css-loader sass-loader node-sass 
```

让我们通过将它们添加到配置中来看看它们是什么。在 webpack 配置的`modeuls/rules`中添加一个新对象。

```
{
    test: /\.scss$/,
    use: [
        "style-loader",
        "css-loader",
        "sass-loader",
    ],
}, 
```

在这里，我们告诉 webpack 获取我们所有的 scss 文件，将它们从 scss 转换为 css(通过 sass-loader)，解析 css 语法，如*导入*或 *url* (通过 css-loader)，然后获取编译后的 css 并将其加载到 html 的 head 标签下的`<style>`中(通过 style-loader)。我们还包含了节点 sass，因为它是 sass 加载器内部工作所必需的。

## 重述

恭喜你，现在你已经有了一个最小的 react 应用程序，它已经启动并运行了。如果你来到这里，你应该得到掌声。

让我们看看到目前为止我们都做了些什么

*   [x]安装了所需的库，如 react、webpack 等。
*   [x]设置代码格式更漂亮
*   [x]添加了 webpack 和基本配置
*   [x]配置了 Babel 并将其连接到 webpack
*   [x]从 webpack 呈现 HTML 的附加插件
*   [x]带 scss 和样式加载器的样式化应用

## 更多设置

围绕 react、webpack 等的社区非常庞大，因此我们有一些非常强大的支持库和插件。让我们看看上面的设置还能做些什么

*   [ ]我们可以添加[单独的 webpack 配置](https://webpack.js.org/configuration/#use-different-config-file)供生产使用。
*   [ ]根据需要添加更多 webpack 插件，例如用于[复制资产文件夹](https://webpack.js.org/plugins/copy-webpack-plugin/#root)、[最小化 css 文件](https://webpack.js.org/plugins/mini-css-extract-plugin/#root)、[添加全局常量](https://webpack.js.org/plugins/define-plugin)、[优化构建](https://webpack.js.org/configuration/optimization/#root)。
*   [ ]让 webpack [代码用分割块分割](https://webpack.js.org/guides/code-splitting/#root)。
*   [ ]有到源文件的[解析路径](https://webpack.js.org/configuration/resolve/#root)，所以我们可以像对 node_modules 那样直接导入。
*   [ ]能够使用[附加的巴别塔插件](https://babeljs.io/docs/en/plugins)编写和传输最新的 ES 代码。
*   [ ]使用您选择的语言为[制作健壮的服务器，为生产代码](https://webpack.js.org/guides/production/#root)提供服务。
*   [ ]以及更多

> 用于生产的配置和构建因应用程序而异。

这篇博文的目的是让你熟悉如何为你的下一个 react 项目设置一个小的、配置更少的 webpack。