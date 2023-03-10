# 如何设置 Webpack 配置📦

> 原文：<https://dev.to/areknawo/how-to-setup-webpack-config--1ooi>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

最近，将资产和代码捆绑在一起已经很常见了。它允许创建可移植的包，不仅易于重用和传输，而且有利于快速交付，从而获得更好的用户体验(性能)。自从发布了 **ES6 模块**规范——为你的 JS 代码提供模块化的标准化方式——以来，它获得了极大的提升。虽然没有被浏览器迅速采用，但它们很快在开发者中流行起来，取代了其他劣质系统，如 **AMD** 和 **CommonJS** 。模块化程度越高，对捆扎机的需求也越大。Webpack 由于其强大的功能性和可扩展性，迅速占据了上风。但是随着插件、扩展、加载器等数量的增加。在您的安排中，为所有具有不同需求的用户提供一个合适的解决方案或更具体的配置并不容易。这就是为什么 Webpack 的配置对一些人来说有点困难和令人疲惫的原因。这就是这个教程存在的原因。在这里，我将尝试向您介绍创建 Webpack 配置的基础知识。我真的建议你从头到尾读完这篇文章，因为最后还有一个**奖**在等着你。😂事不宜迟，我们先来看看 Webpack 本身。

# 网页包&公司

Webpack 被宣传为现代 JavaScript 应用程序的静态模块捆绑器。这是一个流行的捆绑网络应用的工具。由于支持 ES6 模块、CommonJS、AMD 和 **@import** s，它几乎可以处理日常 web 应用程序使用的所有资源。它背后有一个广泛的社区，有一个真正庞大的生态系统，包括用于许多不同资产的**插件**和**加载器**。也就是说，它并不是这项工作唯一合适的工具。还有更多高质量的捆扎机。其中一个是 **Rollup.js** 。它只是另一个捆绑器，但更适合捆绑**库**和其他 JS 工具，而不是 web 应用。这个领域还有一个新玩家叫做 **Parcel.js** 。对于不喜欢配置之类的东西的人来说，这是一个完美的解决方案。Parcel.js 为许多不同的资产和格式提供真正的开箱即用支持。这 3 个是我最喜欢的，虽然肯定还有更多其他的基于工具，但我不会自然地把它们都列出来。🙃现在，您已经知道了可能的替代方案，下面是如何一步一步地配置您的 Webpack。

# 配置

更具体地说，让我们定义我们的配置应该做什么。以下配置应满足我们项目的所有要求。在这种情况下，这将是一个简单的基于**反应**并写入**类型脚本**的 **SPA** 和 **PWA** 。在定义我们的风格时，我们还将使用 **SCSS** (不支持任何 CSS)来获得更好的体验。我们开始吧！😁

看看 Webpack 配置文件的框架。

```
const path = require('path');

module.exports = {
  entry: './src/index.tsx',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].js'
  },
  resolve: {
      extensions: []
  },
  module: {
      rules: []
  },
  plugins: []
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在你有了。我们配置的基本结构。它位于 **webpack.config.js** 文件中，该文件利用 CommonJS 语法导出我们的配置对象。在它里面，我们有相对指向文件的`entry`字段，捆绑器应该从这里开始工作。然后我们有了带有适当的`path`和`filename`的`output`对象，用于生成的包。名称使用**【name】**占位符来表示输出的名称应该对应于我们的模块的名称(默认情况下是 **main** )。`Resolve.extensions`部分基本上是 Webpack 应该读取和处理的一组**文件扩展名**。接下来，我们有`module.rules`，它可以说是整个配置中最重要的地方之一。在这里我们定义了应该处理特定文件的**加载器**。最后是`plugins`字段，所有 Webpack **插件**将在这里找到它们的位置。现在，让我们用一些内容填充它，好吗？

```
// ...
    resolve: {
        extensions: [ '.tsx', '.ts', '.js', '.jsx' ]
    },
    module: {
        rules: [{
            test: /\.tsx?$/,
            use: ['babel-loader', 'ts-loader'],
            exclude: /node_modules/
        }]
    },
// ... 
```

Enter fullscreen mode Exit fullscreen mode

和...这就是处理 TypeScript 所需的全部内容！让我们仔细看看这是怎么回事。在`extensions`中，我们已经添加了我们将来会用到的所有可能的扩展。在`rules`中，我们提供了我们的第一条规则。它是一个具有 3 个属性的对象。`test`是一个正则表达式，匹配所有以**结尾的文件。ts** 或**。tsx** 扩展并使用 **ts-loader** 和`use`字段中提供的 **babel-loader** 进行处理。使用两个处理器使我们能够使用 Babel 处理从 TS 编译器输出的代码。请记住，从数组中提供的最后一个**到第一个**使用加载器。最后，我们将 **node_modules** 排除在匹配之外，因为谁可能需要处理这些并使他的系统滞后呢？😂值得一提的是，您不需要以任何方式要求 ts-loader，只需安装它即可。当我们谈论安装的时候，我可能忘记提到任何关于 Webpack 安装的东西，所以让我们用一个简单的命令来解决这个问题:

```
npm install --save-dev webpack webpack-cli typescript @babel/core babel-loader ts-loader 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们加入对 SCSS 的支持！

```
// ...
{
    test: /\.scss$/,
    use: [
        'style-loader',
        { loader: 'css-loader', options: { importLoaders: 1 } },
        'sass-loader',
    ],
},
// ... 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们需要使用多达 3 个加载器，所以让我们先安装它们，并且不要忘记用于处理 SCSS 的 **node-sass** ！

```
npm install --save-dev node-sass style-loader css-loader sass-loader 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，我们在这里所做的是使用带有 node-sass lib 的 **sass-loader** 处理 SCSS 文件，用 **css-loader** 转换所有的 **@import** 和 **URL** s，并用 **style-loader** 实际使用/插入我们的样式。css-loader 的`importLoaders`选项表示在 CSS 1 之前使用了多少个加载器。在我们的例子中，它只是一个一次性加载器。看看为 loader 提供附加选项的语法。

最后，让我们想象一下，添加对捆绑**图像**又名**静态文件**的支持！

```
npm install --save-dev file-loader 
```

Enter fullscreen mode Exit fullscreen mode

```
// ...
{
    test: /\.(jpe?g|png|gif|svg)$/i,
    loader: 'file-loader'
},
// ... 
```

Enter fullscreen mode Exit fullscreen mode

通过**文件加载器**，Webpack 将每个匹配的导入处理成适当的 URL。请注意，在定义单个加载程序时，可以使用加载程序字段，而不是使用。

另外，不要忘记其他配置文件，比如**ts config . JSON**for[**TypeScript**](http://typescriptlang.org/)...

```
{  "compilerOptions":  {  "outDir":  "./dist/",  "sourceMap":  true,  "noImplicitAny":  false,  "module":  "commonjs",  "target":  "es5",  "jsx":  "react",  "lib":  ["es5",  "es6",  "dom"]  },  "include":  [  "./src/**/*"  ],  } 
```

Enter fullscreen mode Exit fullscreen mode

...还有**。babelrc** 为 [**Babel**](https://babeljs.io/) :

```
npm install --save-dev @babel/preset-env @babel/preset-react @babel/preset-typescript 
```

Enter fullscreen mode Exit fullscreen mode

```
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-react"
  ],
  "env": {
    "development": {
      "presets": ["@babel/preset-typescript"]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我不会涉及这些，因为它们有点跑题，如果你想了解更多，可以查看它们页面的链接——本文中列出的所有工具都有很棒的文档。📙⚡

现在让我们来看看插件。

```
npm install --save-dev clean-webpack-plugin html-webpack-plugin
workbox-webpack-plugin webpack-pwa-manifest 
```

Enter fullscreen mode Exit fullscreen mode

```
const CleanPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const WorkboxPlugin = require('workbox-webpack-plugin');
const WebpackPwaManifest = require('webpack-pwa-manifest');
// ...
plugins: [
    new CleanPlugin(["dist"]),
    new HtmlWebpackPlugin({
        filename: 'index.html',
        title: 'Webpack Config',
        template: './src/index.html'
    }),
    new WebpackPwaManifest({
        name: 'Webpack Config',
        short_name: 'WpConfig',
        description: 'Example Webpack Config',
        background_color: '#ffffff'
    }),
    new WorkboxPlugin.GenerateSW({
        swDest: 'sw.js',
        clientsClaim: true,
        skipWaiting: true,
    })
],
// ... 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们看到了多达 4 个插件！他们每个人都有自己特定的目的。 **Clean-webpack-plugin** 负责清理输出目录——一个简单的任务。 **Html-webpack-plugin** 使用提供的数据和模板文件设置我们的 Html 文件。

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta 
          name="viewport" 
          content="width=device-width, initial-scale=1, shrink-to-fit=no"
        >
    <%= htmlWebpackPlugin.options.title %>
</head>

<body>
    <noscript>
        You need to enable JavaScript to run this app.
    </noscript>
    <div id="root"></div>
</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

那是我们的**模板文件** BTW，标题直接取自插件的配置对象。最后， **workbox-webpack-plugin** 和 **webpack-pwa-manifest** 分别为**离线服务人员**和 **app manifest** 提供 **PWA** 功能。其中一些有很多定制选项，所以如果您计划使用它们，请访问它们的项目页面了解更多信息。

# 生产

在这一点上，我们可以有把握地说，我们的配置是完全可操作的。但这还不够。有了 Webpack，您可以针对不同的用例拥有多个配置。最常见的例子是有两种配置用于**生产**和**开发**，因为每个环境都有其特定的要求。让我们把我们的 webpack.config.js 分成 **3 块**。

**Webpack.common.js** 将包含开发和生产配置相同的配置。

```
const CleanPlugin = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const WorkboxPlugin = require('workbox-webpack-plugin');
const WebpackPwaManifest = require('webpack-pwa-manifest');
const path = require("path");

module.exports = {
  entry: "./src/index.tsx",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "[name].js"
  },
  resolve: {
    extensions: [".tsx", ".ts", ".js", ".jsx"]
  },
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: [
          "style-loader",
          { loader: "css-loader", options: { importLoaders: 1 } },
          "sass-loader"
        ]
      },
      {
        test: /\.(jpe?g|png|gif|svg)$/i,
        loader: "file-loader"
      }
    ]
  },
  plugins: [
    new CleanPlugin(["dist"]),
    new HtmlWebpackPlugin({
        filename: 'index.html',
        title: 'Webpack Config',
        template: './src/index.html',
    }),
    new WebpackPwaManifest({
        name: 'Webpack Config',
        short_name: 'WpConfig',
        description: 'Example Webpack Config',
        background_color: '#ffffff'
    }),
    new WorkboxPlugin.GenerateSW({
        swDest: 'sw.js',
        clientsClaim: true,
        skipWaiting: true,
    })
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们创建我们的 **webpack.prod.js** 配置。我们需要将其与我们的通用配置合并。要做到这一点，我们可以利用**web pack-merge**——一个工具来做到这一点。因此，让我们安装它和其他 2 个插件，我们稍后将使用。

```
npm install --save-dev webpack-merge uglifyjs-webpack-plugin hard-source-webpack-plugin 
```

Enter fullscreen mode Exit fullscreen mode

```
const merge = require('webpack-merge');
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'production',
    devtool: 'source-map',
    module: {
        rules: [{
            test: /\.tsx?$/,
            use: ["babel-loader", "ts-loader"],
            exclude: /node_modules/
        }]
    },
    optimization: {
        minimizer: [new UglifyJsPlugin({
            sourceMap: true
        })],
    },
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们可以看到两个新的属性- `mode`和`devtool`。`Mode`表示我们当前的环境——要么是*【生产】**【开发】*要么是*【无】*。这允许一些工具对所选的 env 应用适当的优化。`Devtool`属性是指生成**源地图**的方式。Webpack 为该属性内置了许多选项。还有许多插件提供额外的功能。但是*“source-map”*选项从内容文件中生成源地图，现在对我们来说已经足够了。然后我们有我们的老式**。ts** 文件加载器。接下来是我们配置中新的、自我解释的字段。`optimization.minimizer`允许我们指定一个插件来最小化我们的文件，这在面向生产时很有用。在这里，我将使用**uglifyjs-web pack-plugin**，这是一款久经考验的产品，具有良好的性能和稳定的输出。不要忘记这个插件的`sourceMap`选项，否则你的源地图**将不会生成**！现在，让我们转到开发配置文件- **webpack.dev.js** 。

```
const merge = require('webpack-merge');
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'eval-source-map',
    module: {
        rules: [{
            test: /\.tsx?$/,
            loader: "babel-loader",
            exclude: /node_modules/
        }]
    },
    plugins: [
        new HardSourceWebpackPlugin()
    ]
}); 
```

Enter fullscreen mode Exit fullscreen mode

在开发时，我们只关心**速度**。此时不需要进行任何优化。我们只想让我们的代码被快速捆绑**。这同样适用于源映射，这一次使用得更快，但不是那么优化的*“eval-source-map”*选项。然后，在为 TypeScript 定义加载器时，我们只使用一个加载器，即 **babel-loader** 。通过这样做，我们只传输我们的。ts 文件**而不进行类型检查**，这对捆绑速度有很大影响。这就是为什么之前我在**中定义了**[@ babel](https://dev.to/babel)/preset-typescript**用于开发阶段。babelrc** 文件。最后，我们有**hard-source-web pack-plugin**，它提供了一个简单的方法来缓存我们的文件，所以我们的第二次捆绑会更快！
还有...就是这样！我们已经准备好可以使用的适当的特定于环境的配置！**

 **# 热重装🔥

所以我们已经有了很好的配置，但是谁需要一个没有**热重装**的快速开发配置呢！？没错——越来越热了！🔥那么，让我们暂时把生产配置放在一边，让我们来实现这个奇妙的特性，好吗？使用 **webpack-dev-server** 真的很简单！你可以用
安装它

```
npm install --save-dev webpack-dev-server 
```

Enter fullscreen mode Exit fullscreen mode

对于配置，将`devServer`配置对象添加到我们的 **webpack.dev.js** 文件中。

```
// ...
devServer: {
    contentBase: path.join(__dirname, 'dist'),
    compress: true,
    port: 9000
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们提供基本选项，如端口、要服务的目录以及是否应该进行压缩。就是这样！最后，让我们为我们的 **package.json** 添加两个**脚本**以便于开发。

```
"scripts":  {  "start":  "webpack-dev-server --config webpack.dev.js",  "build":  "webpack --config webpack.prod.js"  } 
```

Enter fullscreen mode Exit fullscreen mode

通过使用 **- config** 选项，我们提供了特定于 env 的 Webpack 配置的位置。

现在你有了！您自己的 Webpack 配置，支持 TS/TSX、SCSS、优化生产和开发设置以及 HMR！作为一个旁注，我们的 HMR 工作得很好，但当涉及到反应特定的东西时，还有改进的空间。例如，如果您希望在重新加载时保持组件的状态。为此，您可以使用 **react-hot-loader** 并遵循 [**这个令人敬畏的指南**](http://gaearon.github.io/react-hot-loader/getstarted/) ，同时使用您已经在这里创建的配置。

# 送礼🎁

因此，正如您通过阅读本教程所看到的，创建 Webpack 配置并不困难。这只是一个有点耗时的过程，可能需要一些谷歌不时。但对一些人来说也很有趣。但如果你在另一组，我有特别的东西给你。我已经创建了一个简单的 **CLI 工具**，用于为您的 **Webpack 配置**创建**基本样板文件**。通过使用这个，你就不必花时间一遍又一遍地设置同样的东西。它叫做**网络套装**，你可以从 [**NPM**](https://www.npmjs.com/package/webpack-suit-up) 下载。所以，是的，如果你有兴趣就去看看。

我希望这篇教程能帮助你配置你的 Webpack。想了解更多关于 Webpack 的信息，你可以查看它的官方网站。但是，正如我在开始时所说的，有许多其他伟大的工具可能甚至不需要配置。甚至有一些是基于 Webpack 并自动配置它的。还有，即使是来自 **v4** 的 Webpack 本身也不需要配置，但在大多数情况下确实是必须的。也许你想看看有趣的**网络捆绑商**的完整列表？或者说是配置 **Rollup.js** 的指南？写在下面的评论里。**分享这篇文章**，让别人更快发现。此外， **[在 Twitter 上关注我](https://twitter.com/areknawo)** 或在我的脸书页面上关注我了解更多最新内容。😁**