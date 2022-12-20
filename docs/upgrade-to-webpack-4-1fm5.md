# 升级到 Webpack 4

> 原文：<https://dev.to/olegchursin/upgrade-to-webpack-4-1fm5>

我们目前在[怡安网络解决方案](https://www.aon.com/cyber-solutions) (1)开发的应用程序是一个被驱逐的 CRA，带有 TypeScript 和 Apollo GraphQL。令我们高兴的是，我们有`webpack.config.dev.js`和`webpack.config.prod.js`可以一起玩，并且必须确保两个环境都按预期工作。

在兴奋地将我们的故事书更新到 v.5 时，我们发现我们的 Webpack 也需要翻新。所以进入 Webpack 4 时代的时候到了。

访问官方移民指南是你的第一个电话。遵循一步一步的指示无济于事，有些沮丧。谷歌搜索，StackOverflowing 和 GitHubbing 救援。好吧，好吧…我不是唯一沮丧的人。

下面是一个循序渐进的指南，引导我们走向成功的`yarn start`和`yarn build`:

### 第一步。升级 webpack 并安装 webpack-cli:

```
yarn add webpack
yarn add webpack-cli 
```

Enter fullscreen mode Exit fullscreen mode

版本检查:

```
//  package.json  ...  "webpack":  "^4.29.6",  "webpack-cli":  "^3.2.3",  ... 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步。在`webpack.config.dev.js`和`webpack.config.prod.js`中添加各自的模式

```
// webpack.config.dev.js
...
module.exports = {
    mode: 'development',
    ... 
```

Enter fullscreen mode Exit fullscreen mode

```
// webpack.config.prod.js
...
module.exports = {
    mode: 'production',
    ... 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步。添加 fork-ts-checker-notifier-web pack-plugin

```
yarn add fork-ts-checker-notifier-webpack-plugin --dev 
```

Enter fullscreen mode Exit fullscreen mode

版本检查:

```
//  package.json  ...  "fork-ts-checker-notifier-webpack-plugin":  "^1.0.0",  ... 
```

Enter fullscreen mode Exit fullscreen mode

### Step 4.1 更新 html 网页包插件

```
yarn add html-webpack-plugin@next 
```

Enter fullscreen mode Exit fullscreen mode

版本检查:

```
//  package.json  ...  "html-webpack-plugin":  "^4.0.0-beta.5",  ... 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 4.2 调整`webpack.config.dev.js`和`webpack.config.prod.js`中的插件顺序

确保`HtmlWebpackPlugin`在`InterpolateHtmlPlugin`之前，并且后者的声明如下例所示:

```
plugins: [
  new HtmlWebpackPlugin({
    inject: true,
    template: paths.appHtml
  }),
  new InterpolateHtmlPlugin(HtmlWebpackPlugin, env.raw),
... 
```

Enter fullscreen mode Exit fullscreen mode

### 第五步。更新`ts-loader`、`url-loader`和`file-loader`

```
yarn add url-loader file-loader ts-loader 
```

Enter fullscreen mode Exit fullscreen mode

版本检查:

```
//  package.json  ...  "file-loader":  "^1.1.11",  "ts-loader":  "4.0.0",  "url-loader":  "0.6.2",  ... 
```

Enter fullscreen mode Exit fullscreen mode

### 第六步。更新反应-开发工具

```
yarn add react-dev-utils 
```

Enter fullscreen mode Exit fullscreen mode

版本检查:

```
//  package.json  ...  "react-dev-utils":  "6.1.1",  ... 
```

Enter fullscreen mode Exit fullscreen mode

第七步。用`mini-css-extract-plugin`代替`extract-text-webpack-plugin`

完全去掉`extract-text-webpack-plugin`。添加并配置`mini-css-extract-plugin`。

```
yarn add mini-css-extract-plugin 
```

Enter fullscreen mode Exit fullscreen mode

版本检查:

```
//  package.json  ...  "mini-css-extract-plugin":  "^0.5.0", 
```

Enter fullscreen mode Exit fullscreen mode

配置:

```
// webpack.config.prod.js
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

plugins: [
  ...
  new MiniCssExtractPlugin({
    filename: "[name].css",
    chunkFilename: "[id].css"
  }),
  ...
],
module: {
  rules: [
    {
      test: /\.css$/,
      use: [
      {
        loader: MiniCssExtractPlugin.loader,
        options: {
        // you can specify a publicPath here
        // by default it use publicPath in webpackOptions.output
        publicPath: '../'
      }
    },
    "css-loader"
  ]
}, 
```

Enter fullscreen mode Exit fullscreen mode

### 第八步。更新和重新配置`uglifyjs-webpack-plugin`

```
yarn add uglifyjs-webpack-plugin --dev 
```

Enter fullscreen mode Exit fullscreen mode

版本检查:

```
//  package.json  ...  "uglifyjs-webpack-plugin":  "^2.1.2" 
```

Enter fullscreen mode Exit fullscreen mode

配置:

```
// webpack.config.prod.js
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
module.exports = {
  ...
  optimization: {
    minimizer: [new UglifyJsPlugin()],
  },
... 
```

Enter fullscreen mode Exit fullscreen mode

第九步。修复您可能遇到的任何弃用错误。遵循错误消息，跟踪并更新您可能需要的任何 webpack 插件。在我们的例子中是`webpack-manifest-plugin`。

我真的希望这个指南至少能为某人节省几分钟/几个小时。

* * *

(1)这是一篇个人博客文章，绝不反映怡安网络解决方案的官方观点。

* * *

**用于提出工作解决方案的来源:**

*   [https://webpack.js.org/migrate/4/](https://webpack.js.org/migrate/4/)
*   [http://garyzhangblog.cn/2018/12/31/20181231webpack4/](http://garyzhangblog.cn/2018/12/31/20181231webpack4/)
*   [https://github . com/Realytics/fork-ts-checker-web pack-plugin/issues/154](https://github.com/Realytics/fork-ts-checker-webpack-plugin/issues/154)
*   [https://github.com/TypeStrong/ts-loader/issues/726](https://github.com/TypeStrong/ts-loader/issues/726)
*   [https://github . com/web pack/web pack/issues/6244](https://github.com/webpack/webpack/issues/6244)
*   [https://github . com/web pack-contrib/mini-CSS-extract-plugin/tree/b 653641 e 7993 EB 28 fad 70 c 1733 DC 45 FEA 93 c 5](https://github.com/webpack-contrib/mini-css-extract-plugin/tree/b653641e7993eb28fad70c1733dc45feafea93c5)
*   [https://webpack.js.org/plugins/uglifyjs-webpack-plugin/](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/)
*   [https://thebrainfiles . wear ebrain . com/moving-from-web pack-3-to-web pack-4-F8 cdacd 290 f 9](https://thebrainfiles.wearebrain.com/moving-from-webpack-3-to-webpack-4-f8cdacd290f9)
*   [https://github . com/web pack/web pack/issues/6568](https://github.com/webpack/webpack/issues/6568)
*   [https://gist . github . com/gricard/e 8057 f 7 de 1029 f 9036 a 990 af 95 c 62 ba 8](https://gist.github.com/gricard/e8057f7de1029f9036a990af95c62ba8)
*   [https://www.npmjs.com/package/webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server)
*   [https://github.com/webpack-contrib/mini-css-extract-plugin](https://github.com/webpack-contrib/mini-css-extract-plugin)
*   [https://www.npmjs.com/package/file-loader](https://www.npmjs.com/package/file-loader)
*   [https://www.npmjs.com/package/webpack-manifest-plugin](https://www.npmjs.com/package/webpack-manifest-plugin)