# 梦之队——与电子反应

> 原文：<https://dev.to/mitchpierias/the-dream-teamreact-with-electron-474d>

所以你要么是一个涉足电子世界的 React 开发者，要么正在寻求扩展电子的能力。不管怎样，你已经在这里结束了，因为你问了自己同样的问题，把我带进了兔子洞，“我如何在电子内部使用反应？”。

在这个例子中，我不会做什么特别的东西。老实说，我在用 React 编译自己的电子项目时遇到了麻烦，所以我把一切都简化成了这个样板模板。在这个例子中，我们只是让 React 编译我们的基本项目，然后使用 electronic 构建我们的应用程序发行版。这个例子的完整代码可以在 GitHub 上找到[。](https://github.com/MitchPierias/React-Electron-Boilerplate)

## 创建项目

在开始构建我们的电子应用程序之前，我们首先需要安装一些依赖项并定义我们的项目结构。首先，为您的项目创建一个新目录并在里面导航，然后运行`npm init`并填写提示来创建您的项目样板文件。

```
mkdir reactron && cd reactron
npm init 
```

现在我们准备开始安装我们的依赖项，比如 React 和 React DOM！我们还将 path 作为一个生产依赖项进行安装，因为我们稍后将在 Electron 中使用它。

```
npm install --save react react-dom path 
```

我不打算查看 React 代码，这可能需要很长时间，取决于我们选择走多远。所以我假设你知道基本知识。如果没有，你可以从 [GitHub repo](https://github.com/MitchPierias/React-Electron-Boilerplate) 中复制`./src`目录下的文件，如果你想了解更多，可以跟随[这些教程中的一个](https://www.google.com/search?q=Learn+React)。

## Webpack 设置

现在我们需要将 React 项目编译成一个包，为此我们将使用 Webpack。

```
npm install --save-dev webpack webpack-cli 
```

我们还需要 webpack 加载器，如 babel、样式加载器和 html 编译器，让我们安装它们并深入研究它们的功能。

```
npm install --save-dev babel-core@6 babel-loader@7 babel-preset-env@1 babel-preset-react@6 
```

Babel 是负责将我们所有新奇的新时代 Javascript 解析成当前支持的规范 ES5 的加载器。

*   **babel-core:** 将你的 ES6 代码转换成 ES5。
*   babel-loader: Webpack helper 插件，用于将 ES6 依赖项解析为 ES5。
*   **babel-preset-env:** 决定使用哪些转换/插件和聚合填充。
*   **babel-preset-react:** 所有 react 插件的预置。

```
npm install --save-dev css-loader style-loader html-webpack-plugin 
```

*   **style-loader:** 编译 React 组件中引用的 css 文件，并将它们注入到构建文件中。
*   **css-loader:** 是帮助 Webpack 解释和转换 css 的解析器。
*   **html-webpack-plugin:** 输出一个编译后的 html 文件，其中链接了资源和标签。

现在会有大量的依赖建立起来…不要担心，这只是因为网络包的可扩展性。让我们定义我们的项目结构，并开始编码我们的资源，您的项目应该类似于如下所示；

```
Reactron
|- assets
  ...react-assets...
|- build
  |- bundle.js
  |- index.html
|- dist
  ...electron-build-files...
|- node_modules
  ...installed-modules...
|- src
  |- index.js
  |- index.html
  |- index.css
|- package.json
|- main.js
|- webpack.config.js 
```

## 配置网络包

现在，我们准备开始配置 Webpack，将我们的 React 项目和资源捆绑到我们的`./build`目录中，供 electronic 使用。首先，打开`webpack.config.js`并导入 webpack、path 和`html-webpack-plugin`模块。

```
const webpack = require('webpack');
const path = require('path');
const HtmlWebPackPlugin = require("html-webpack-plugin"); 
```

### HTML 网页包插件

html-webpack-plugin 将在 webpack 完成它的魔法后负责编译我们的条目`index.html`文件。

```
const htmlPlugin = new HtmlWebPackPlugin({
 title: "Reactron",
 template: "./src/index.html",
 filename: "./index.html"
}); 
```

然后我们将配置我们的`html-webpack-plugin`。这将通过 Webpack 构建过程将资源和数据注入到我们的`./src/index.html`模板中，将编译后的结果和捆绑的 Javascript 一起输出到输出目录中。

### web pack 构建管道

现在我们终于准备好配置我们的 Webpack 构建管道了。在我们刚刚写的所有内容下面，定义以下内容:

```
module.exports = {
 entry: './src/index.js',
 target: 'electron-renderer',
 output: {
  path: path.resolve(__dirname, 'build'),
  publicPath: './',
  filename: 'bundle.js'
 },
 module: {
  rules: [
   {
    test: /\.js$/,
    exclude: [
     /node_modules/,
     /.json?/
    ],
    use: {
     loader: 'babel-loader',
     query: {
      presets: ["env","react"]
     }
    }
   }, {
    test: /\.(s*)css$/,
    use: ['style-loader','css-loader']
   }
  ]
 },
 plugins:[
  htmlPlugin
 ],
 resolve: {
  extensions: ['.js','.jsx']
 }
} 
```

我不想深入研究 Webpack，但是如果你想进一步研究它，你可以在这里阅读官方文档和例子。这段代码实际上是告诉 Webpack 我们的入口文件在哪里，我们要输出到哪里，以及使用什么模块和插件。

## 电子

我们现在已经用 React 构建了我们的电子呈现器，与 Webpack 捆绑在一起，现在我们准备开始编码我们的电子应用程序来服务于这一切。

```
npm install --save-dev electron 
```

### 编码我们的电子申请

让我们打开`./main.js`文件，复制到下面；

```
const { app, BrowserWindow } = require('electron');
const path = require('path');

let mainWindow = null;

function createWindow() {

 mainWindow = new BrowserWindow({
  width:600,
  height:400
 });

 mainWindow.loadFile(path.resolve(__dirname,'build/index.html'));

 mainWindow.on('closed', () => {
  if (process.platform !== 'darwin') app.quit();
 });
}

app.on('ready', createWindow); 
```

这是启动电子应用程序的绝对最小代码。我们正在从 electron 导入我们的模块路径以及 app 和 BrowserWindow 组件，然后在将它附加到`app.on(‘ready’)`事件之前定义一个空的主窗口引用和`createWindow()`函数。我们的 c `reateWindow`函数正在初始化一个新的`BrowserWindow`，加载我们之前用 Webpack 编译的`./build/index.html`文件，并处理`mainWindow.on(‘closed’)`事件。
运行应用程序

就是这样！我们可以执行`npm webpack --mode production`来编译，然后执行`npm electron .`来启动我们的电子应用程序。让我们通过用 electronic-builder 将所有内容编译成一个发行版来完成我们的项目。

## 电子建造者

```
npm install --save-dev electron-builder 
```

### 构建属性

我们的大部分电子构建器配置发生在我们的`package.json`中的`build`属性内。

```
"build": {
  "productName":"Reactron",
  "appId":"com.reactron.app",
  "directories": {
    "buildResources":"build",
    "output":"dist"
  }
  ...
} 
```

我们定义了自己的`productName`和`appId`，前者是不言自明的，后者通常是一个惟一的标识符，就像 web 上的 URL 一样。重要的属性是目录，在这里你可以指定`buildResources`，Electron 将在那里找到你的捆绑 Webpack 输出，以及定义输出构建的目录的输出设置。

```
"directories": {
  ...
},
"files": [
  "**/*",
  "build/**/*",
  "node_modules/**/*"
] 
```

现在，我们指定要扫描的文件并打包到我们的应用程序包中，我们准备好运行了！

```
node electron-builder 
```