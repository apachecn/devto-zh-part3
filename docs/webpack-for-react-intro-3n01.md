# React 网络包(简介)

> 原文：<https://dev.to/vish448/webpack-for-react-intro-3n01>

## 什么是 webpack？

Webpack 的核心是一个代码捆绑器。它获取你的代码，转换并捆绑它，然后返回一个全新版本的代码。

## 它在解决什么问题？

想想我们有多少次不得不修改我们的代码，使其符合浏览器的习惯(普通的 HTML、CSS 和 JavaScript)。如果你曾经使用过像 SASS 或 LESS 这样的 CSS 预处理器，你应该知道你需要把你的 SASS/LESS 代码转换成普通的 CSS。

Webpack 真正的亮点在于，你可以告诉它你的代码需要做的每一个转换，它会执行这些转换，并为你输出一个包含这些更改的包文件(如果你愿意，还可以输出一些其他有用的东西，比如缩小)。

## web pack 应该知道的三件事

*   webpack 需要知道您的应用程序的起始点，或者您的根 JavaScript 文件。
*   webpack 需要知道对代码进行哪些转换。
*   webpack 需要知道它应该将新转换的代码保存到哪个位置。

我们需要做的第一件事是创建一个包含我们的 webpack 配置的文件。为了方便起见，这个文件应该命名为 webpack.config.js，并位于我们项目的根目录中。

现在我们已经创建了文件，我们需要确保该文件导出一个对象，该对象将代表我们对 webpack 的配置。

```
// In webpack.config.js
module.exports = {}
</code></pre>

First tell our webpack an entry point in our app
<pre><code>
module.exports = {
    entry: './app/index.js',
} 
```

Enter fullscreen mode Exit fullscreen mode

我们所做的就是给我们的对象一个 entry 属性和一个指向我们应用程序中的根 JavaScript 文件的字符串值。

既然我们已经告诉 webpack 从哪里开始，我们需要告诉它实际上要进行哪些转换。这就是装载机派上用场的地方。

```
module.exports = {
  entry: './app/index.js',
  module: {
    rules: [
      { test: /\.coffee$/, use: "coffee-loader" }
    ]
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

它告诉 webpack 在所有以. coffee 结尾的扩展名上运行 coffee-loader。

最后一步是指定 webpack 应该在哪里输出新转换的代码。

```
module.exports = {
  entry: './app/index.js',
  module: {
    rules: [
      { test: /\.coffee$/, use: "coffee-loader" }
    ]
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'index_bundle.js'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的代码非常简单明了。filename 是 webpack 将要创建的文件的名称，该文件包含我们新转换的代码。path 是放置新文件名(index_bundle.js)的特定目录

所以现在当 webpack 运行时，我们的代码将被转换，然后可以在 ourApp/dist/index_bundle.js 中引用。

## 挑战做完这一切后

如果你看到我们的迷你文件夹结构后，做了这一切是

```
 /app
        components
        utils
      index.js
      index.html
    /dist
      index.html
      index_bundle.js
    package.json
    webpack.config.js
    .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们正在开发的代码位于 app 文件夹中，而我们转换后的代码位于 dist 文件夹中。现在，您可以直观地看到问题。我们希望更改位于 app 文件夹中的 index.html，但浏览器实际要使用的 index.html 文件位于 dist 文件夹中(因为我们也已经告诉 webpack 在那里显示转换后的 JS 文件)。

## 多一步

它并没有真正复制我们的 index.html 文件，而是将该文件用作模板，并创建一个全新的 index.html 文件。这个插件是 html-webpack-plugin。和往常一样，在使用它之前，您需要运行 NPM install-save-dev html-web pack-plugin。现在我们只需要告诉 webpack 我们想用它做什么。

首先，我们需要创建一个 HTMLWebpackPlugin 的新实例，并指定一个东西，即我们希望新创建的文件看起来像什么的模板。

```
module.exports = {
  entry: './app/index.js',
  module: {
    rules: [
      { test: /\.coffee$/, use: "coffee-loader" }
    ]
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'index_bundle.js'
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: 'app/index.html'
    })
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们从命令行运行 webpack，在我们的 dist 文件夹中，我们将有两个文件。index_bundle.js 和 index.html。index_bundle.js 是获取我们的入口代码并通过我们的加载器运行它的结果。而 index.html 是用 HTMLWebpackPluginConfig 动态创建的，它是位于我们的应用程序文件夹中的原始 index.html 文件的副本，带有引用新创建的 index_bundle.js 文件的脚本标签

现在让我们看看 webpack 运行
app/index.html
后的 index.js 文件

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  My App
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div id="app"></div>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

dist/index.html

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  My App
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div id="app"></div>
  <script src="index_bundle.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到这两个文件之间唯一的区别是 dist 中的文件(它是用 HTMLWebpackPlugin 创建的)现在有一个指向 index_bundle.js 的脚本标记。同样，这里唯一真正神奇的是，HTMLWebpackConfig 足够智能，可以从 Webpack 中检测到您新创建的文件的输出文件名，它会自动将其作为脚本添加到您新创建的 index.html 文件中。因此，在我们的例子中，我们使用 index_bundle.js 作为输出文件名，所以你可以在上面创建的 index.html 文件中看到，我们现在在主体中有。如果我们要将我们的 webpack 配置的输出改为我们的-AWESOME-JS-FILE.js，那么在我们新创建的 index.html 文件的主体中，我们将有

最后，从 Webpack 4 开始，回到我们的 webpack.config.js 文件中，我们需要告诉它我们希望它以哪种“模式”运行——“生产”或“开发”。现在，我们只需将模式设置为“开发”。这将使调试工具和更快的构建成为可能。

```
module.exports = {
  entry: './app/index.js',
  module: {
    rules: [
      { test: /\.coffee$/, use: "coffee-loader" }
    ]
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'index_bundle.js'
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: 'app/index.html'
    })
  ],
  mode: "development"
} 
```

Enter fullscreen mode Exit fullscreen mode

为了做到这一点，你需要安装两个东西。“webpack”和“webpack-cli”。安装完成后，您将能够向运行 webpack 的 package.json 添加一个新脚本。

```
"scripts": {
  "build": "webpack"
}, 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以在你的终端上运行“npm run build ”,它将一次性运行你的设置，然后编译你的代码并输出到一个 dist 文件夹中。然而，每当您更改任何内容时，都必须一遍又一遍地运行该命令，这可能是一种痛苦。要解决此问题，请在您的 NPM 脚本中更改 webpack 以运行 webpack -w，这将监视您的文件，并在 webpack 关注任何文件更改时重新执行 webpack。最后，如果您想发布到产品中，您可以运行 webpack -p，它将通过正常的转换运行，并缩减您的代码。