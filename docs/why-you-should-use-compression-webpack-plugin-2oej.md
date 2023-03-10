# 为什么你应该使用压缩 Webpack 插件？

> 原文：<https://dev.to/slashgear_/why-you-should-use-compression-webpack-plugin-2oej>

如果您使用 webpack 作为应用程序的模块捆绑器，您可能会担心它本身的性能和加载时间。你可能使用了代码分裂、树摇动、丑化，但是你可能忘记了一个非常重要的步骤。

## 压缩是强制性的

在 2019 年，如果你没有使用压缩算法以更轻的版本交付文件，以使你的网站加载更快，情况就很危急。每个网站性能分析器都会告诉你这样做。这是一个普通的有 Gzip 和没有 Gzip 的网站的大小比较。

 [<source type="image/webp">
<source type="image/png">
![Vue CLI vanilla size compared to gzip](img/0b0d3968af049b8dacc53ab8e572f8b0.png "Vue CLI vanilla size compared to gzip")](///static/15d620b40e592ba0943d30e7a40c5dc2/0a47e/vue-cli-vanilla-size.png) 

这里我们用 Gzip 压缩了实际大小的 64%。

## 动态压缩静态文件是多余的

每一个现代的网络服务器都有可能按需压缩文件。虽然 Gzip 速度很快，但是按需压缩静态文件是反模式的。即使您在静态文件请求前使用了缓存。

静态文件压缩应该在构建期间完成。

如果您在 webpack 环境中，添加压缩非常容易。

为此，您可以使用
安装*压缩-web pack-插件*

```
npm install --dev compression-webpack-plugin 
```

Enter fullscreen mode Exit fullscreen mode

…并将其添加到您的生产配置中:

```
...
plugins: [
    new CompressionWebpackPlugin(),
]
... 
```

Enter fullscreen mode Exit fullscreen mode

通过两个简单的步骤，您的输出静态文件将拥有它们的*。gz* 当量。现在，如果你把这些文件放在像 [Caddy](https://caddyserver.com/) 这样的网络服务器上。如果存在的话，它会自动提供预压缩版本。然而你需要在 Nginx 服务器配置中激活它[。](http://nginx.org/en/docs/http/ngx_http_gzip_static_module.html)

## “我们只应生成。gz？”

如果您遵循了上述步骤，您就在 web 服务器上启用了压缩。太棒了！

**但在 2019 年，Gzip 并不是压缩你的文件的唯一解决方案。**

### 佐普利

Zopfli 是一种无损压缩算法，与压缩时间较慢的 Gzip 相比，它具有更好的压缩结果。所以如果可以的话，你必须总是对静态文件使用 Zopfli 压缩，而不是基本的 Gzip。Zopfli 的输出文件遵循与 Gzip 相同的格式，浏览器支持也是一样的。

您可以将 zopfli [与压缩 webpack 插件一起使用。](https://github.com/webpack-contrib/compression-webpack-plugin#using-zopfli)

### 布罗特利

Brotli 是最近的压缩算法( [RFC 7932](https://tools.ietf.org/html/rfc7932) )和[一个伟大的浏览器支持](https://caniuse.com/#search=brotli)。基于新的上下文建模和新的文件格式，brotli 非常适合压缩静态文件。brotli 仍然有一些困难的部分(在节点< v11 上不可用，在一些 web 服务器上很难配置，比如 Nginx)

您可以将 brotli [与 compression webpack 插件一起使用。](https://github.com/webpack-contrib/compression-webpack-plugin#using-zopfli)

 [<source type="image/webp">
<source type="image/png">
![Vue CLI compression results](img/2338f79f1b4f8acd6217eebab384b9ff.png "Vue CLI compression results")](///static/0d631442de390a6737ca967f30f3263d/416ee/vue-cli-complete-size.png) 

## 总结:“为什么要用 Webpack 做？”

在构建步骤中压缩静态文件仍然是一个有争议的话题。有些人认为压缩文件是由网络服务器决定的。事实上，今天的 web 服务器并没有真正优化静态文件的压缩。

在构建过程中压缩时:

*   您可以很容易地看到您的 web 应用程序的完整大小(当您想要跟踪 web 应用程序的性能时，这非常有用)
*   你的应用程序可移植性更强(你可以用预压缩的资产轻松地改变你的部署或你的 web 服务器)
*   作为一名前端开发人员，你应该熟悉无损压缩的巨大价值。