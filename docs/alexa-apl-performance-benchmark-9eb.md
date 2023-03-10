# Alexa APL 性能基准

> 原文：<https://dev.to/willblaschko/alexa-apl-performance-benchmark-9eb>

[![Falling Blocks Game](img/87c2b3dbe475587bf442c16588819fcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9PW3OzZw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/alexa-posts/ss_performance.png) 
*(以上是用于压力测试 APL 的布局)*

反问:你有没有想过测试不同 Alexa [APL 文档](https://developer.amazon.com/docs/alexa-presentation-language/apl-document.html)的加载时间？为了能够 A/B/C/…测试它们以了解哪种实现将为您的客户提供最快的渲染时间？

希望答案是:“绝对可以！”

这里有一些代码，你可以用它们在浏览器和物理设备上进行基准测试。

**要求:**

*   NodeJS 脚本已配置([要点](https://gist.github.com/willblaschko/a0e361e8e5e3afb3799a31165c362729)
*   您可以保存[木偶师](https://github.com/GoogleChrome/puppeteer)用户数据(登录 cookies)的本地文件夹
*   [用 Alexa 点燃电视棒](https://www.amazon.com/Fire-TV-Stick-with-Alexa-Voice-Remote/dp/B0791TX5P5)(可选)
*   [ADB 安装](https://developer.android.com/studio/command-line/adb)(如果在消防电视上进行基准测试)

**工作原理:**
木偶师打开显示器[模拟器](https://developer.amazon.com/alexa/console/ask/displays)，反复渲染布局/数据。每次浏览器渲染布局时，还会(可选地)通过 ADB 向 Fire TV 发送一条命令。该脚本通过监听 web 控制台日志(模拟器)和 ADB 日志(Fire TV)来计算每次渲染的时间。

**如何使用:**

1.  配置 index.js 以满足您的需求，目前一切都在代码中定义，但它可以被修改为命令行参数。
2.  如果使用 Fire TV， *adb 将…* 连接到目标设备。
3.  将一个有效的 Chromium 用户目录复制到一个可访问的文件夹中，并用作一个参数(或者将 benchmark.js 改为自动登录)。

**这是什么:**
这是我优化自己的文档时的一个概念验证实验。我希望有人能以此为起点，开发一个更全面的工具。

**文件:**
[index.js](https://gist.github.com/willblaschko/a0e361e8e5e3afb3799a31165c362729#file-index-js) -启动基准并输出性能
[benchmark.js](https://gist.github.com/willblaschko/a0e361e8e5e3afb3799a31165c362729#file-benchmark-js) -基准库
[package.json](https://gist.github.com/willblaschko/a0e361e8e5e3afb3799a31165c362729#file-package-json) -节点包
[layout _ base . JSON](https://gist.github.com/willblaschko/a0e361e8e5e3afb3799a31165c362729#file-layout_base-json)-用于重置模拟器的默认布局
layout_example_*。json -不同的布局基准
[Data _ example . JSON](https://gist.github.com/willblaschko/a0e361e8e5e3afb3799a31165c362729#file-data_example-json)-用于所有布局的数据

**样本输出:**
运行布局 0 基准(浏览器)
基准运行 100 次。
平均渲染:1723 毫秒
最大渲染:3989 毫秒
最小渲染:236 毫秒

运行布局 1 的基准(浏览器)
基准运行了 100 次。
平均渲染:1786 毫秒
最大渲染:3326 毫秒
最小渲染:224 毫秒