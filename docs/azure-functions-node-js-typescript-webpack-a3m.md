# azure Functions+node . js+type script+web pack

> 原文：<https://dev.to/dnasir/azure-functions-node-js-typescript-webpack-a3m>

我是打字稿的忠实粉丝。如果我的博客文章和 GitHub 项目还没有说清楚的话，我现在把它记录下来。

因此，当我发现最新的`azure-functions-core-tools`现在能够开箱即用地用 TypeScript 创建一个函数应用程序时，我很兴奋。

在这篇文章中，我想分享我在 TypeScript 中为 Node.js 设置 Azure Functions 应用程序项目的经验，并使用 Webpack 来生成应用程序包。

## 设置

你首先需要在全球范围内安装`azure-functions-core-tools`。虽然这不是绝对必要的，因为您也可以将它安装到一个空文件夹中，并使用`npx`助手执行命令。这就是我所做的，因为我的主要项目仍然在 Azure Functions 1.x 运行时上运行，当时我不想做任何改变。

1.  `func init`启动向导。这将把项目安装到当前目录。
2.  将工人运行时间设置为`node`。
3.  将语言设置为`typescript`。

恭喜你。您刚刚在 TypeScript 中创建了一个 Azure Functions 项目。

在幕后，它使用`tsc`来编译 TypeScript 文件，这些文件放在`dist`文件夹中。`function.json`已经设置为使用最近添加的`scriptFile`属性指向编译后的代码，该属性告诉 Azure Functions 运行时在运行 Function 应用程序时在哪里找到代码。

就这样，对吧？干得好，对吧？呃，没有。

## 路径/别名

随着我的函数项目变得越来越大，我开始不得不在多个函数间共享代码，因此我开始在我的`tsconfig.json`文件中使用`paths`属性来处理模块别名。

但是我一把`../../common/sharedModule`换成`common/sharedModule`，我的功能应用就停止工作了。`Unable to find module`它说。

原来，`tsc`不处理模块解析。它不会将别名转换成它们对应的路径，TypeScript 团队也无意实现这个特性，正如这个[旧 GitHub 问题](https://github.com/Microsoft/TypeScript/issues/10866)中所描述的。

解决方案？

## Webpack 所有的东西！

[![Meme image of Webpack All The Things!](img/9d1483ac3014b02888e3925b47f78237.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1Hsym6n4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.dnasir.com/assets/img/2019/webpack-all-the-things.jpg)

如果您愿意，也可以使用其他捆扎机。我只是喜欢 Webpack，所以我将在这个项目中使用它。

首先，你需要 [Webpack](https://www.npmjs.com/package/webpack) (咄)，和 [`ts-loader`](https://www.npmjs.com/package/ts-loader) 来将类型脚本代码转换成普通的 JavaScript。

接下来，您需要向您的项目添加一个`webpack.config.js`。

这里有一个示例配置文件。

```
const path = require('path');
const TsconfigPathsPlugin = require('tsconfig-paths-webpack-plugin');

module.exports = {
  target: 'node', // IMPORTANT!
  entry: {
    QueueTrigger: path.resolve(__dirname, './QueueTrigger/index.ts')
  },
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/
      }
    ]
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
    plugins: [
      new TsconfigPathsPlugin()
    ]
  },
  output: {
    filename: '[name]/index.js',
    path: path.resolve(__dirname, 'dist'),
    libraryTarget: 'commonjs' // IMPORTANT!
  }
}; 
```

### `target`

确保输出`target`被设置为`node`，以确保代码被编译为在 Node.js 环境中工作。默认值是`web`，某些库，比如 [Axios](https://www.npmjs.com/package/axios) ，依赖于此来决定在编译期间应该加载哪个版本的代码，而使用错误的代码会有一些可怕的后果，正如我逐渐发现的那样。

我的项目依赖于 Axios，在将构建目标设置为`node`之前，我的函数应用程序一直抛出关于`XMLHttpRequest`未定义的错误。这是因为 Webpack 为`XMLHttpRequest`实际存在的 web 环境构建了项目，而不是为它不存在的节点环境构建项目。

将构建目标设置为`node`修复了这个问题。

### `libraryTarget`

您需要将输出`libraryTarget`设置为`commonjs`,以确保入口点的默认导出是输出模块的默认导出。如果没有这个，当你运行你的 Azure Functions 应用时，你会得到下面的错误。

```
[error] Worker was unable to load function QueueTrigger: 'Unable to determine function entry point. If multiple functions are exported, you must indicate the entry point, either by naming it 'run' or 'index', or by naming it explicitly via the 'entryPoint' metadata property.' 
```

### `tsconfig-paths-webpack-plugin`

下一部分是可选的，但对我的情况至关重要。我想使用别名，这样我就可以避免使用相对路径来解析模块路径。为此，我使用了 [`tsconfig-paths-webpack-plugin`](https://www.npmjs.com/package/tsconfig-paths-webpack-plugin) 来使用我在`tsconfig.json`文件中设置的别名。

或者，您也可以在您的`webpack.config.js`文件中这样设置:

```
module.exports = {
  resolve: {
    alias: {
      'common': path.resolve(__dirname, './common')
    }
  },
}; 
```

但是谁会想这么做呢，对吧？

### 更新项目 NPM 脚本

最后，您将希望更新项目`package.json`文件中定义的 NPM 脚本，特别是`build`和`watch`脚本。

```
{
  "scripts": {
    "build": "webpack --mode=production",
    "watch": "webpack --mode=development --watch"
  }
} 
```

所以下次运行`npm run start`，它将启动 Azure Functions 项目，同时运行 Webpack watcher。

应该注意的是，为了让运行时获得更改，您需要确保将`dist`文件夹添加到监视列表中。这是在`host.json`文件中完成的。

```
{
  "watchDirectories": [
    "dist"
  ]
} 
```

现在，每当 Webpack 重新构建代码并更新`dist`文件夹中的文件时，Azure Functions 运行时都会检测到这一点，并继续重新启动 Functions 应用程序。

## 部署

有很多方法可以将你的功能应用部署到 Azure，正如在[官方 Azure Functions 文档](https://docs.microsoft.com/en-us/azure/azure-functions/)中的操作指南>部署下所描述的。我选择了 GitHub 的[连续部署](https://docs.microsoft.com/en-us/azure/azure-functions/functions-continuous-deployment)选项。

在设置了我的部署配置之后，Azure 自动运行了部署作业。太棒了。

不幸的是，在部署过程的最后，我发现自己的 Azure Functions 应用程序没有工作。

怎么了，阿祖尔？

事实证明，虽然文档声明当找到一个`package.json`文件时会触发 [`npm install`，但是 Azure 不会自动运行构建作业。根据他们的文档，](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#dependency-management) [transpilation 是在主机初始化之前和部署过程中完成的](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#typescript)。我怀疑 transpilation 过程失败的原因可能与上面提到的原因相同。

解决方案？NPM 剧本。

虽然我还没有找到在部署过程中手动调用 NPM 命令的方法，但我确实知道当调用`npm install`时， [NPM 有多个事件](https://docs.npmjs.com/misc/scripts)可以挂接到其中。所以我决定挂接到`postinstall`事件来调用我的构建任务。

```
{
  "scripts": {
    "postinstall": "npm run build",
    "build": "webpack --mode=production"
  }
} 
```

这确保了项目是在部署期间构建的，果然，我现在看到我的新 Azure 函数列在函数列表下。

## 结论

Azure 函数现在支持开箱即用的 TypeScript，这太棒了。然而，虽然他们目前的实现对于简单的应用程序来说可能已经足够好了，但他们仍然缺少一些功能，最明显的是别名。幸运的是，通过结合使用 webpack 和 NPM 脚本管道，我能够克服这些障碍。

我希望这能帮助其他面临同样问题的人。