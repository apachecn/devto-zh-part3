# 网络中的地铁捆扎机

> 原文：<https://dev.to/sasurau4/metro-bundler-in-web-1385>

## [T1】简介](#intro)

你知道麦德龙·邦德勒吗？如果你使用 React Native，你会很熟悉它。
Metro Bundler 是 React Native 的 JavaScript bundler。
几个月前，我发现了一条关于 Metro Bundler 的令人惊讶的事情的推文。

> ![](img/f0effb829a865dd209de81797293a7ba.png)![](img/2eb71326af8add7772f61b476b1655d6.png)文森特·里默尔@ vincentrimer![](img/4d9c44713c216584b3d48ff3455cbb68.png)我发现了 [@cpojer](https://twitter.com/cpojer)
> 
> 看起来网络上的 Instagram 正在被 [@MetroBundler](https://twitter.com/MetroBundler) 捆绑！2019 年 1 月 25 日上午 02:44[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1088628642970783744)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1088628642970783744)9[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1088628642970783744)33

> ![](img/0bc586c49e14365411b4b44add12c617.png)克里斯托夫·中泽友秀@ CPO jer![](img/4d9c44713c216584b3d48ff3455cbb68.png)[@ vincentrimer](https://twitter.com/vincentriemer)[@ metro bundler](https://twitter.com/MetroBundler)巨大如果真的话。08:12am-2019 年 1 月 25 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1088711186424758272)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1088711186424758272)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1088711186424758272)13

> ![](img/db21c9ada501983816cc75be7562c75c.png)格伦·康纳@ Mr _ sharpoblunto![](img/4d9c44713c216584b3d48ff3455cbb68.png)[@ CPO jer](https://twitter.com/cpojer)[@ vincentrimer](https://twitter.com/vincentriemer)[@ metro bundler](https://twitter.com/MetroBundler)The secrets out:)下午 15:46-2019 年 1 月 25 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1088825340829421569)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1088825340829421569)0

哈哈哈，我简直不敢相信！
因为[文档](https://facebook.github.io/metro/)顶部写着“React Native 的 JavaScript Bundler *”。
但是中泽友秀女士和康纳先生是脸书团队的成员，而康纳先生是 Instagram 网站基金会的技术主管。因此，我检查了 Instagram 是否使用 metro bundler 进行 web。*

## 检查

Metro Bundler 有[关于使用 CLI](https://facebook.github.io/metro/docs/en/cli) 的文档，所以我捆绑了一些 JavaScript 文件，并与 web 的捆绑文件进行比较。
我发现了类似`__BUNDLE_START_TIME__=this.nativePerformanceNow?nativePerformanceNow():Date.now()`的共同点。在 Chrome 的开发者工具控制台输入`window._sharedData`，你会发现`bundle_variant: "metro"`似乎是捆绑配置。

我确认了 Instagram 将 Metro Bundler 用于 Web。我很好奇是怎么做到的。让我们来试试吧！

## 在 Web 中使用 Metro Bundler！

回购:[https://github.com/sasurau4/sample-metro-bundler-in-web](https://github.com/sasurau4/sample-metro-bundler-in-web)

我解释如何在 Web 中使用 Metro Bundler。

根据文档的[，Metro Bundler 提供其作为 CLI、服务器本身和 express 中间件的功能。](https://facebook.github.io/metro/docs/en/getting-started.html)

所以，我选择了 express 中间件，服务器紧随其后。

```
const Metro = require('metro');
const express = require('express');
const app = express();

Metro.loadConfig().then(async config => {
  const metroBundlerServer = await Metro.runMetro(config);
  app.use(metroBundlerServer.processRequest.bind(metroBundlerServer));

  app.use('/', express.static('public'));

  const { server: serverConfig } = config;
  const { port } = serverConfig;
  app.listen(port);
}); 
```

该服务器提供`public`目录中的静态文件和 Metro 捆绑的捆绑文件。

静态文件如下。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    Metro Bundler in Web!
  </head>
  <body>
    <div id="root"></div>
    <script src="index.bundle?platform=web&dev=true&minify=false"></script>
  </body>
</html> 
```

我们可以从路径`index.bundle`中获得带有各种参数的包文件。

如果你想只捆绑文件，这一切都完成了。

在现实世界中，我们经常需要用 babel 之类的东西来传输 JS 文件。

配合 Metro 使用 Babel 非常容易。

如果你想使用 React，把`babel.config.js`像下面这样。

```
module.exports = {
  presets: ['@babel/preset-env', '@babel/preset-react'],
  plugins: ['@babel/plugin-proposal-export-default-from'],
}; 
```

运行服务器并访问本地主机，应用程序在浏览器中运行！都完成了。🎉

我解释如何像使用 webpack-dev-server 一样使用 Metro Bundler。如果您想将工件部署到托管服务，您可以从 cli 完成！

## 结论

虽然 Metro Bundler 主要针对 React Native，但我们也可以将其用于 web。我还学习了 webpack 和 webpack-dev-server 提供的功能。
如今在生产中很少使用 Metro Bundler for web。
我想象如果有一天 Metro Bundler 被经常用于 web 成为现实，我们可以用 React Native 和 React Native Web 编写通用的应用程序。
令人兴奋的未来！

感谢 Metro 团队提供的文档和维护。
感谢阅读！