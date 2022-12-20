# 发布 NPM 模块的输出目标是什么？

> 原文：<https://dev.to/joelnet/what-is-your-output-target-for-publishing-npm-modules-5h4b>

将 Webpack 与`babel-loader`一起使用时，导入的 NPM 模块不会被混淆。这是由于一个常见的`exclude`规则。

```
/*
 * webpack.config.js
 */
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$|\.jsx$/,
        use: "babel-loader",
        exclude: /node_modules/,
      }
    ]
  }
}; 
```

因此导入的模块必须已经处于最兼容的状态。根据您的 web 应用程序，这可能意味着不同的事情。

例如，如果您的 web 应用程序需要支持 IE11，那么发布的模块也必须支持 IE11。

您可以确保您的模块支持 IE11，但是如果一个 web 应用程序需要支持更多的环境呢？

支持回到文明时代的环境也将膨胀你的 NPM 模块，使它对所有非 IE11 应用程序来说更慢。

另一个选择是让你的模块的用户也 Babel 它，如果他们需要额外的支持。

我不得不这样做`strict-uri-encode` :

```
 exclude: /node_modules\/(?!(strict-uri-encode)\/).*/ 
```

这似乎也不实际。特别是如果一个 NPM 模块依赖于你的 NPM 模块，该模块将不得不决定要么巴贝尔你的模块，以符合其输出目标，或者也泡沫这一决定，他们的 NPM 模块的用户。

你也可以采取“尽力而为”的方法。也就是将你的代码捆绑到一个合理支持的目标上，然后让用户知道如果他们想要更多，就在他们身上。

```
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": "8.10"
        }
      }
    ]
  ]
} 
```

在`README.md`中

```
# Module Target

This module targets ES6 and specifically node 8.10\. If you need to target other environments, you are responsible for Babeling this module. 
```

我也看过 [@pika/pack](https://github.com/pikapkg/pack) ，还没玩过，但是值得一提。

我也看过`esnext`，连同`main`加到`package.json`。还在研究这个。

值得一读:[通过 NPM](http://2ality.com/2017/06/pkg-esnext.html)-2 能力交付未受污染的源代码

我目前采用这种“尽力而为”的方法。

你在发表的 NPM 项目中做了什么？