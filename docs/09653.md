# 如何在 Netlify 上使用 Pug？

> 原文：<https://dev.to/spekulatius1984/how-to-use-pug-on-netlify-3le0>

Netlify 是一个强大而又易于使用的网站托管平台。结合这与帕格，它给你一个伟大的起点来建立和主持自己的网站。

## 什么是哈巴狗？

Pug 或 PugJS，以前叫 Jade，是一个 JavaScript 模板引擎。它从不同的文件组装和组合你的最终 HTML，给你一个比普通 HTML 更干净的开发体验。

## 如何在 Netlify 上配置 pug？

Netlify 允许你在建立网站时使用许多工具。Pug 不支持开箱即用，但可以通过 npm 轻松添加和配置。下面的命令安装 pug，运行文件夹`pug`中所有 pug 文件的编译，并将结果保存在文件夹`public` :
中

```
npm install pug-cli; node node_modules/pug-cli/index.js pug/ --out ./public 
```

Enter fullscreen mode Exit fullscreen mode

您必须根据需要调整此命令，以匹配文件夹，并将其添加到您的 Netlify 站点上的“Build 命令”中:

[![Netlify configuration panel](img/ad4389dda410d41a747f9cef113c148c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EKL-mbR7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://peterthaleikis.com/statimg/How-to-use-Pug-on-Netlify/screenshot.png)

这种方法应该适用于任何可以安装 npm 并直接在命令行上运行的库。它只需要找到正确的文件来执行。