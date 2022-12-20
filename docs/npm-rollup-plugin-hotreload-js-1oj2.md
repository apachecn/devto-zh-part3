# 我的第一个 NPM 包:rollup-plugin-hotreload.js

> 原文：<https://dev.to/tingwei628/npm-rollup-plugin-hotreload-js-1oj2>

[npm 包链接](https://www.npmjs.com/package/rollup-plugin-hotreload)

[![meme](img/4927e2aa5f97a34a57697313451b6490.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RZKsREJn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2s3lai.jpg)

* * *

### *我为什么要这样做*

有几个原因:

首先，当进行前端开发时，我按下`F5`按钮来刷新窗口，看看浏览器中发生了什么。由于这种无意义的行为，我想出了替代的方法来修复这种不便(这类似于自动刷新的概念)。
等？！Webpack 已经有了 hotreload 的特性。为什么我不直接用它呢？但是，我用了 Rollup。而且当时好像没有类似的。好吧，我会继续努力的。

其次，这是一个提高我开发 JavaScript 应用程序技能的机会。

最后，我将实现我的 npm 包被多次下载的目标。太棒了。

### *我面临了什么困难*

如果你对 GitHub 上的[问题感兴趣](https://github.com/tingwei628/rollup-plugin-hotreload/issues?q=is%3Aissue+is%3Aclosed)

首先，我如何从服务器向客户机发送通知？我直觉地想要使用`Socket.IO`，但是与我的项目相比，它的尺寸太大了。所以我查了一下 [webpack-hot-middleware](https://github.com/webpack-contrib/webpack-hot-middleware) ，决定用`SSE`(服务器发送事件)！

其次，我发现监视文件服务器使用的端口与 api-server 使用的端口冲突，因为 Node 向我显示了这样的错误日志“结束后写入”。在浏览了 StackOverflow 上的页面后，我分离了两个端口来解决这个问题。您可以检查这个[拉取请求](https://github.com/tingwei628/rollup-plugin-hotreload/pull/9)

第三，我发现需要通过`__dirname`来避免“没有找到文件”的错误，你可以检查[这个问题](https://github.com/tingwei628/rollup-plugin-hotreload/issues/8)

### *随意的想法*

1.  做部分 bundle 来热重装而不是所有 bundle 热重装怎么样(？
2.  用`node-glob`分配`Bundle`路径怎么样(？
3.  用 WebAssembly 插件怎么样(？

### 更新

2021-07-17:本项目不再维护。:)