# 将类型脚本连接到 React

> 原文：<https://dev.to/gilfink/wiring-typescript-into-react-ebf>

几周前，我在 ReactNext 2018 大会上发表了一场与这篇文章同名的演讲。由于我在会议中对这个话题和问题有很多兴趣，我决定把我在会议中提到的细节放在这篇博文中。

[![Image result for typescript react](img/4556daa45dad3e3c76e4549d8a847d7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JFdK5IHP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2ACHH1P0NwJfZzXXHo-VUzOA.png)

在这篇文章中，我将只解释如何将 TypeScript 添加到现有的 React 应用程序中的技术内容。如果您希望了解使用 TypeScript 的原因或关于 create-react-app 中的 TypeScript 支持，您可以在此处观看会议记录:

[https://www.youtube.com/embed/nI6eD6pFGdA](https://www.youtube.com/embed/nI6eD6pFGdA)

#### 我们在努力做什么？

如果您正在启动一个新项目，create-react-app 已经包含了使用 TypeScript 启动项目的选项。为此，只需运行带有-scripts-version = react-scripts-ts 标志的 create-react-app，您的项目将会使用为您连接的 TypeScript 创建。从那时起，您就可以继续开发您的应用程序了。

但是我们大多数人并不是每天都在开始新的项目。这意味着要么你现在维护一个 React 应用程序，要么开发一个没有 TypeScript 的 React 应用程序。如果您想在现有应用程序中添加 TypeScript 支持，该怎么办？接下来的步骤将帮助您做到这一点。

#### 添加 TypeScript 和 TypeScript Webpack 加载器

首先，您将向项目中添加 TypeScript。打开命令行，进入项目的根文件夹。运行以下命令安装 TypeScript 和 awesome-typescript-loader:

```
npm install --save-dev typescript awesome-typescript-loader 
```

awesome-typescript-loader 是一个常用的 Webpack 加载器，它能够编译和添加 TypeScript transpiled 文件到您的 JavaScript Webpack 构建中。

注意:如果你没有使用 Webpack，那么在你的项目中添加一个 bundler loader，它可以将 TypeScript 转换成 JavaScript。

您可以注意到，我没有全局安装 TypeScript(使用-g 标志)。原因是在每个项目中我都想管理我的 TypeScript 版本。如果定义文件中存在版本不匹配或重大更改，则依赖全局安装可能会引入 TypeScript 编译错误。因为 TypeScript 和 awesome-typescript-loader 都用于开发，所以我使用— save-dev 标志来添加依赖项作为开发依赖项。

#### 添加反应类型

在你安装了 TypeScript 和它的加载程序之后，是时候给你的项目添加类型了。每个主要的库/框架都有 TypeScript 类型，TypeScript 使用这些定义文件来理解所有的库/框架类型系统。这些定义文件以. d.ts 作为后缀，可以使用 npm 从“@types”存储库中下载。React、React-DOM 和 PropTypes 都有类型，所以为了安装它们，使用下面的命令:

```
npm install --save @types/react @types/react-dom @types/prop-types 
```

如果你有其他的依赖，也添加它们的类型。当您使用 React 或任何其他库/框架时，省略添加类型的步骤可能会导致编译错误。

#### 添加 TypeScript 配置文件

现在项目中已经有了 TypeScript 和相关的类型，可以继续配置 TypeScript 编译器了。为了配置 TypeScript 编译器，项目的根文件夹中需要有一个 tsconfig.json 文件。添加新文件，并在项目中放置编译器配置选项。以下代码是 tsconfig.json 文件的一个示例:

```
{
 "compilerOptions": {
 "outDir": "./dist/",
 "sourceMap": true,
 "module": "commonjs",
 "target": "es5",
 "jsx": "react"
 },
 "include": [
 "./src/\*\*/\*"
 ]
} 
```

对于 React 开发，您应该添加到 compilerOptions 部分的唯一重要配置是“jsx”:“React”(在示例中用粗体标记)。此选项有助于 TypeScript 编译器理解 React 组件中使用的 JSX。

#### 配置 Webpack 构建 TypeScript

要在应用程序中启用 TypeScript，您必须做的最后一件事是在 Webpack 中添加一个构建步骤，将 TypeScript 代码转换为 JavaScript 代码。

*注意:如果您使用另一个捆扎机，将您在第一步中安装的加载器连接到捆扎机构建中。在这个例子中，我展示了如何用 Webpack 来做这件事。*

为了将 TypeScript 添加到 Webpack 构建过程中，您将在 Webpack 规则中添加 awesome-typescript-loader 加载程序。你应该测试。ts 和。tsx 文件，并使用 awesome-typescript-loader 将这些文件编译成 JavaScript。webpack.config.js 文件的示例如下:

```
const path = require('path');
const webpack = require('webpack');

module.exports = {
 entry: './index.js',
 mode: 'development',
 output: {
   path: path.resolve(\_\_dirname, 'build'),
   filename: 'bundle.js'
 },
 resolve: {
   extensions: [".ts", ".tsx" , ".js", ".json"]
 },
 module: {
   rules: [
     { test: /\.tsx?$/, loader: "awesome-typescript-loader" } ,
     { test: /\.js$/, loader: 'babel-loader', query: { presets: ['es2015', 'react'] }}
   ]
 },
 stats: {
   colors: true
 },
 devtool: 'source-map'
}; 
```

我已经用粗体标记了应该添加到配置文件中的内容。

完成所有步骤后，您的项目应该可以使用 TypeScript 了。

恭喜你！

继续使用添加 React 组件。tsx 文件，然后编译该项目并运行它，以测试一切正常。

#### 总结

在这篇文章中，我解释了如何将 TypeScript 添加到现有的使用 Webpack 的 React 应用程序中。主要是，您将向项目添加 TypeScript，向 Webpack(或您的项目捆绑器)添加 TypeScript 构建步骤，并在您的应用程序中使用 TypeScript。

如果您有任何意见，请随时在评论区添加:)