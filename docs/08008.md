# 安装 React、Babel 7 和 Webpack 4

> 原文：<https://dev.to/0xhjohnson/setup-react-babel-7-and-webpack-4-2168>

[创建 react 应用](https://github.com/facebook/create-react-app)让 React 提供合理的默认设置变得非常容易。然而，您将到达一个点，您需要，或者可能只是想，从零开始设置。

> “你不能信任的代码是你不理解的代码。反之亦然:你不理解的代码是你不能信任的代码。”- Kyle Simpson，功能轻 JavaScript

# 先决条件

在我们开始之前，你必须安装你选择的包管理器，npm 默认情况下随 node.js 一起提供。

# 设置

让我们首先创建一个新目录来存放我们的 React 应用程序，并初始化一个 package.json 文件来管理我们的依赖项等...

`mkdir default && cd default && yarn init -y`

# 安装通天塔

Babel 需要一些软件包来正确地翻译 JSX 代码。

1.  [@babel/core](https://babeljs.io/docs/en/babel-core) -核心 babel 编译器
2.  [@ babel/CLI](https://babeljs.io/docs/en/babel-cli)-babel 命令行
3.  [@babel/preset-env](https://babeljs.io/docs/en/babel-preset-env) -允许通过 Browserslist 定位浏览器的智能预设
4.  [@babel/preset-react](https://babeljs.io/docs/en/babel-preset-react) -为 JSX 等提供支持...
5.  允许使用 babel 和 Webpack 传输 JS 文件

`yarn add @babel/core @babel/cli @babel/preset-env @babel/preset-react babel-loader -D` 

# 创建通天塔配置文件

我们现在准备创建一个. babelrc 配置文件。这是我们将告诉 Babel 使用我们刚刚安装的插件的地方。

```
{  "presets":  ["@babel/preset-env",  "@babel/preset-react"]  } 
```

# 安装网页包

Webpack 只需要几个包就可以开始了。

1.  [webpack](https://webpack.js.org/api/) -自我解释的核心包，负责捆绑您的资产
2.  webpack-cli -提供一套工具来改进 webpack 配置设置
3.  webpack-dev-server -提供一个支持热重载的开发服务器
4.  [html-webpack-plugin](https://webpack.js.org/plugins/html-webpack-plugin/) -生成包含 webpack 包的 index.html 文件
5.  [html-webpack-template](https://www.npmjs.com/package/html-webpack-template) -将元素 id 附加到挂载 Javascript 到 etc 的能力...`yarn add webpack webpack-cli webpack-dev-server html-webpack-plugin html-webpack-template -D`

# 创建 Webpack 配置文件

Webpack 4 不需要配置文件，但在大多数情况下，您会希望使用配置文件。我们将创建一个 webpack.config.js 文件来存储我们的配置。该配置指定我们的源文件位于/src 目录中，并将我们的 Javascript 包作为 bundle.js 输出到/dist 目录。HtmlWebpackPlugin 在/dist 中创建我们的 index.html 文件，并在包外包含脚本标记。

```
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const HtmlWebpackTemplate = require('html-webpack-template');
const path = require('path');

const config = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        use: 'babel-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: ['.js', '.jsx'],
  },
  plugins: [
    new HtmlWebpackPlugin({
      inject: false,
      template: HtmlWebpackTemplate,
      appMountId: 'app',
    }),
  ],
};

module.exports = config; 
```

# 向 package.json 添加脚本

我们现在准备定义一些 npm 脚本来运行 Webpack，它将使用 Babel 来传输我们的代码。

```
{  "name":  "default",  "version":  "1.0.0",  "main":  "index.js",  "license":  "MIT",  "scripts":  {  "start:dev":  "webpack-dev-server",  "build":  "webpack"  },  "devDependencies":  {  "@babel/cli":  "^7.4.3",  "@babel/core":  "^7.4.3",  "@babel/preset-env":  "^7.4.3",  "@babel/preset-react":  "^7.0.0",  "babel-loader":  "^8.0.5",  "html-webpack-plugin":  "^3.2.0",  "html-webpack-template":  "^6.2.0",  "webpack":  "^4.29.6",  "webpack-cli":  "^3.3.0",  "webpack-dev-server":  "^3.2.1"  },  "dependencies":  {  "react":  "^16.8.6",  "react-dom":  "^16.8.6"  }  } 
```

# 在 src 内部创建 index.js 文件

让我们通过创建一个包含基本 React 组件的 Javascript 文件来测试我们的配置。创建一个名为/src 的文件夹来存放我们所有的源文件。在/src/index.js 中包含一些代码，作为我们应用程序的入口点。因为我们在 Webpack 配置中指定了 appMountId，所以我们可以将我们的 app 组件呈现给 Id 为 app 的元素。我们还需要安装 React 和 React-DOM。
T2`yarn add react react-dom`

```
import React from "react";
import ReactDOM from "react-dom";

function App() {
  return (
    <div>
      <h1>Hello blog readers</h1>
    </div>
  );
}

const rootElement = document.getElementById("app");
ReactDOM.render(<App />, rootElement); 
```

最后，我们需要启动我们的开发服务器并测试我们的配置。
`yarn run start:dev`