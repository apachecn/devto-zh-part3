# 为项目设置 webpack 4

> 原文：<https://dev.to/teroauralinna/setting-up-webpack-4-for-a-project-5f5e>

这篇博文展示了如何为开发设置 webpack 4 模块捆绑器。本 webpack 教程包含了许多在使用 webpack 进行 JS 应用程序开发时可能需要配置的常见示例。

在我的 [GitHub 库](https://github.com/teroauralinna/webpack-guide)中可以获得这个演示。该演示包括几个示例组件。这些组件真的没有做任何合理的事情。它们只是为了证明当我们添加新的东西时配置的改变是有效的。

我意识到我从来没有真正自己配置过整个 webpack 开发工作流程。通常在你开始使用像 Vue.js 或者 Angular 这样的 JS 框架的时候就已经做好了。这就是这篇博文的灵感来源。

## 涵盖以下任务

1.  设置 webpack-dev-server 和 npm 构建脚本
2.  添加 index.html 和生成的 Javascript 包
3.  添加 webpack 别名以便于导入文件
4.  用巴别塔把 ES6 转换成 ES5
5.  导入和注入 CSS 代码
6.  将所有 CSS 提取到一个文件中
7.  通过文件加载器处理文件
8.  内嵌 SVG 元素
9.  通过 postcss-loader 和 autoprefixer 应用 CSS 供应商前缀
10.  通过缩小来优化 CSS 和 Javascript 资源
11.  将 TypeScript 与@babel/preset-typescript 一起使用
12.  独立的开发和生产环境

## 先决条件

以下是我们开始填充的最低配置。此外，您应该在启动之前安装 [Node.js](https://nodejs.org/en/) 。

**webpack.config.js**

```
const path = require('path');

module.exports = {
  entry: './src/app',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'app.[contenthash:8].js',
    publicPath: '/'
  },
  resolve: {
    modules: [
      'node_modules',
      path.resolve(__dirname, 'src')
    ],
    extensions: ['.js'],
  }
} 
```

**package . JSON**T2】

```
{  "name":  "webpack-guide",  "version":  "1.0.0",  "description":  "webpack 4 guide",  "main":  "app.js",  "dependencies":  {  },  "devDependencies":  {  },  "author":  "John Doe",  "license":  "ISC"  } 
```

## 设置 webpack-dev-server 和 npm 构建脚本

**安装[web pack-dev-server](https://github.com/webpack/webpack-dev-server)**

```
$ npm i webpack-dev-server webpack-cli webpack --save-dev 
```

**将以下 npm 脚本添加到 package.json**

```
 "scripts":  {  "build":  "rm -rf ./dist/ && webpack --mode production --config webpack.config.js",  "dev":  "webpack-dev-server --mode development --config webpack.config.js"  } 
```

在我们添加了`./src/app.js`文件之后，就有可能第一次构建我们的应用了。App.js 是我们 App 的入口。

## 添加 index.html 并生成 Javascript 包

尽管在浏览器中仍然没有任何东西可以显示。因此，让我们添加`index.html`文件，并将生成的 JS 包添加到该文件中。这可以用 [html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin) 来完成。

安装 html 网页包插件

```
$ npm i html-webpack-plugin --save-dev 
```

**创造 index.html**创造 T2】

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <%= htmlWebpackPlugin.options.title %>
  <link href="https://fonts.googleapis.com/css?family=Source+Sans+Pro:400,600" rel="stylesheet">
  <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>

</body>
</html> 
```

**将配置添加到 webpack.config.js 中**

```
const HtmlWebpackPlugin = require('html-webpack-plugin');

...

  plugins: [
    new HtmlWebpackPlugin({
      title: 'Setting up webpack 4',
      template: 'index.html',
      inject: true,
      minify: {
        removeComments: true,
        collapseWhitespace: true
      },
    })
  ] 
```

现在我们可以用`npm run dev`启动应用程序，并导航到地址 [http://localhost:8080](http://localhost:8080) 。我们将看到标题为**设置 webpack 4** 的空白页。

## 添加 webpack 别名使导入文件更容易

有了 alias，我们就不必使用大部分时间都很烦人的相对导入路径了。

例如，我们可以使用`import { header } from '@components'`来代替`import { header } from '../../components'`。

**webpack.config.js**

```
 resolve: {
    alias: {
      '@components': path.resolve(__dirname, 'src/components'),
      '@scss': path.resolve(__dirname, 'src/scss'),
      '@img': path.resolve(__dirname, 'src/img'),
      '@': path.resolve(__dirname, 'src')
    }
  } 
```

## 用巴别塔将 ES6 转化为 ES5

我想写 ES6 而不是旧的 Javascript 语法，所以让我们为传输文件添加 [Babel](https://babeljs.io) config。

**安装通天塔和通天塔装载机**

```
$ npm i babel-loader @babel/core @babel/preset-env --save-dev 
```

**将配置添加到 webpack.config.js 中**

```
 module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /(node_modules)/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
    ]
  } 
```

## 导入和注入 CSS 代码

为了导入和使用 CSS 样式，我们需要添加新的加载器。 [Css-loader](https://github.com/webpack-contrib/css-loader) 将内容导入变量， [style-loader](https://github.com/webpack-contrib/style-loader) 将内容作为内嵌标签注入 HTML 文件。

**安装**

```
$ npm i style-loader css-loader --save-dev 
```

**将配置添加到 webpack.config.js 中**

```
 {
        test: /\.css$/,
        use: [
          "style-loader", 
          "css-loader"
        ]
      } 
```

为了支持 SCSS，我们将添加`sass-loader`和`node-sass`。

**安装 sass-loader 和 node-sass**T2】

```
$ npm i sass-loader node-sass --save-dev 
```

**将 sass-loader 添加到现有风格配置块**

```
 {
        test: [/.css$|.scss$/],
        use: [
          "style-loader", 
          'css-loader', 
          'sass-loader'
        ]
      } 
```

## 将所有 CSS 提取到一个文件中

现在我们可以设计我们的应用程序了。样式加载器样式作为内联注入。如果我们想使用外部样式表文件，我们可以用 [css-mini-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin) 提取样式。然后这个样式表将被自动注入到`index.html`中。

**安装**

```
$ npm i mini-css-extract-plugin --save-dev 
```

**webpack.config.js**

```
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

...

      {
        test: [/.css$|.scss$/],
        use: [
          MiniCssExtractPlugin.loader,
          "css-loader", 
          "sass-loader"
        ]
      }
...

  plugins: [
    ...
    new MiniCssExtractPlugin({
      filename: 'app.[contenthash:8].css',
    }),
    ...
  ] 
```

## 通过文件加载器导入图像

为了包含图像，我们需要配置[文件加载器](https://github.com/webpack-contrib/file-loader)。

**安装文件加载器**

```
$ npm i file-loader --save-dev 
```

**webpack.config.js**

```
 {
        test: /\.(png|jpg|gif|svg)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[hash:8].[ext]',
              outputPath: 'assets/'
            }
          }
        ]
      } 
```

现在可以通过导入
来使用图像

```
import nodejsLogo from '@img/nodejs.png' 
```

或者 CSS 规则。

```
body {
  background: transparent url(../img/webpack-logo.png);
} 
```

## 内嵌 SVG 元素

在某些情况下，我们可能想要内嵌资产。这是内联 SVG 图像的配置。

**安装[SVG-URL-loader](https://github.com/bhovhannes/svg-url-loader)T3** 

```
$ npm i svg-url-loader --save-dev 
```

**webpack.config.js**

```
 {
        test: /\.svg$/,
        loader: 'svg-url-loader',
        options: {
          noquotes: true
        }
      }, 
```

还要从文件加载器配置中删除 svg 扩展。

## 通过 postcss-loader 和 autoprefixer 应用 CSS 厂商前缀

供应商前缀可以通过 [postcss-loader](https://github.com/postcss/postcss-loader) 和 [autoprefixer](https://github.com/postcss/autoprefixer) 自动应用于样式。

**安装 postcss-loader 和 autoprefixer**

```
$ npm i postcss-loader autoprefixer --save-dev 
```

**将配置添加到 webpack.config.js 中**

```
 {
        test: [/.css$|.scss$/],
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader', 
          'sass-loader',
          'postcss-loader'
        ]
      } 
```

**创建 postcss.config.js**

```
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
} 
```

**将目标浏览器添加到包中. json**

```
 "browserslist":  [  "> 1%",  "last 2 versions",  "not ie <= 8"  ] 
```

更改后，所有供应商前缀将自动设置为需要自动前缀的样式。

您可以通过 package.json 中的`browserslist`属性调整浏览器支持。在[browser 1 . ist](https://browserl.ist/?q=%3E+1%25%2C+last+2+versions%2C+not+ie+%3C%3D+8)通过不同的规则查看支持的浏览器。

## 通过缩小优化 CSS 和 Javascript 资产

然后，让我们通过缩减资产来优化应用程序。实际上，webpack 4 在使用`production`模式时会默认优化 JS 包。如果你想调整设置，你可以自己提供一个插件。

**安装插件**

```
$ npm i uglifyjs-webpack-plugin optimize-css-assets-webpack-plugin --save-dev 
```

**将配置添加到 webpack.config.js 中**

```
const UglifyJsPlugin = require("uglifyjs-webpack-plugin");
const OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");

...

  optimization: {
    minimizer: [
      new UglifyJsPlugin(),
      new OptimizeCSSAssetsPlugin()
    ]
  }, 
```

## 配合@babel/preset-typescript 使用 TypeScript

有一种新方法可以将 TypeScript 与 Babel 一起使用。这篇博文“[与巴别塔的打字稿:一场美丽的婚姻](https://iamturns.com/typescript-babel/)”很好地解释了利弊，所以我在这里不再重复。

另一种选择是使用 [ts-loader](https://webpack.js.org/guides/typescript/) 。

**安装 TypeScript 和@ babel/preset-TypeScript**

```
$ npm i @babel/preset-typescript typescript --save-dev 
```

**修改 webpack.config.js 中的 babel-loader 设置，以包含@babel/typescript 预设**

```
 {
        test: [/.js$|.ts$/],
        exclude: /(node_modules)/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [
              '@babel/typescript', 
              '@babel/preset-env'
            ]
          }
        }
      },

  ...

  resolve: {
    extensions: [".js", ".ts"],
  }, 
```

**添加 custom.d.ts**

```
declare module "*.svg" {
  const content: any;
  export default content;
}

declare module "*.png" {
  const content: any;
  export default content;
} 
```

**添加 tsconfig.json 进行类型脚本设置**

```
{  "compilerOptions":  {  //  Target  latest  version  of  ECMAScript.  "target":  "esnext",  //  Search  under  node_modules  for  non-relative  imports.  "moduleResolution":  "node",  //  Process  &  infer  types  from  .js  files.  "allowJs":  true,  //  Don't  emit;  allow  Babel  to  transform  files.  "noEmit":  true,  //  Enable  strictest  settings  like  strictNullChecks  &  noImplicitAny.  "strict":  true,  //  Disallow  features  that  require  cross-file  information  for  emit.  "isolatedModules":  true,  //  Import  non-ES  modules  as  default  imports.  "esModuleInterop":  true,  "baseUrl":  ".",  "paths":  {  "@components":  ["src/components"],  "@scss":  ["src/scss"],  "@img":  ["src/img"],  "@":  ["src"],  }  },  "include":  [  "custom.d.ts",  "src"  ]  } 
```

别名也必须添加到`tsconfig.json`路径中，以便 TypeScript 可以找到它们。

**将检查类型脚本添加到 package.json**

```
 "scripts":  {  "check-types":  "tsc"  } 
```

现在您有了一个单独的类型检查命令。

```
$ npm run check-types -- --watch 
```

您还可以将`npm run check-types`添加到您的构建脚本中，以便在为生产构建时检查类型。

现在您可以将您的`.js`文件重命名为`.ts`，并开始使用 TypeScript 特性。我的演示项目包含 js 和 ts 文件，这在真实项目中是不应该的。

注意，类型检查不是这种方法的开发工作流的一部分。你需要分别检查它们。这可能是件好事也可能是件坏事，取决于你想如何工作。

## 分离开发和生产环境

最后，让我们对构建系统做一些更改。我们将把开发和生产构建分开，以使开发更容易，构建更快。

**安装 web pack-合并**

```
$ npm i webpack-merge --save-dev 
```

**创建 build/web pack . base . config . js**

我们将把大部分配置移到这个文件中。

```
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module.exports = {
  entry: './src/app',
  output: {
    path: path.resolve(__dirname, '../dist'),
    filename: 'app.[contenthash:8].js',
    publicPath: '/'
  },
  module: {
    rules: [
      {
        test: [/.js$|.ts$/],
        exclude: /(node_modules)/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [
              '@babel/typescript', 
              '@babel/preset-env'
            ]
          }
        }
      },
      {
        test: /\.svg$/,
        loader: 'svg-url-loader',
        options: {
          noquotes: true
        }
      },
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[hash:8].[ext]',
              outputPath: 'assets/'
            }
          }
        ]
      },
      {
        test: [/.css$|.scss$/],
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader', 
          'sass-loader',
          'postcss-loader'
        ]
      }
    ]
  },
  resolve: {
    alias: {
      '@components': path.resolve(__dirname, '../src/components'),
      '@scss': path.resolve(__dirname, '../src/scss'),
      '@img': path.resolve(__dirname, '../src/img'),
      '@': path.resolve(__dirname, '../src')
    },
    modules: [
      'node_modules',
      path.resolve(__dirname, '../src')
    ],
    extensions: ['.js', '.ts'],
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'app.[contenthash:8].css',
    }),
    new HtmlWebpackPlugin({
      title: 'Setting up webpack 4',
      template: 'index.html',
      inject: true,
      minify: {
        removeComments: true,
        collapseWhitespace: true
      },
    })
  ]
} 
```

**创建 build/webpack.dev.config.js**

开发配置目前很空，但可能会出现一些应该只添加到开发环境中的东西。

```
const merge = require('webpack-merge')
const webpackBaseConfig = require('./webpack.base.config.js')

module.exports = merge(webpackBaseConfig, {}) 
```

**创建 build/web pack . prod . config . js**

生产文件有所有的优化任务，这将减缓我们的开发建设。尽管记得偶尔测试产品构建以避免早期的产品配置相关问题。

```
const merge = require('webpack-merge')
const UglifyJsPlugin = require('uglifyjs-webpack-plugin')
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')
const webpackBaseConfig = require('./webpack.base.config.js')

module.exports = merge(webpackBaseConfig, {
  optimization: {
    minimizer: [
      new UglifyJsPlugin(),
      new OptimizeCSSAssetsPlugin()
    ]
  }
}) 
```

**修改 package.json 构建命令以利用新配置**

```
 "scripts":  {  "build":  "rm -rf ./dist/ && npm run check-types && webpack --mode production --config ./build/webpack.prod.config.js",  "dev":  "webpack-dev-server --mode development --config ./build/webpack.dev.config.js",  "check-types":  "tsc"  }, 
```

在这一步之后，可以从项目的根中删除`webpack.config.js`。我把它保存在演示中作为参考。

* * *

现在我们的 webpack 配置看起来已经准备好了，我们可以更多地关注应用程序逻辑了。

* * *

这篇博文最初发表于[aural linna . blog](https://auralinna.blog/post/2018/setting-up-webpack-4-for-a-project)