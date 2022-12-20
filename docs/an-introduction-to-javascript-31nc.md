# JAVASCRIPT 简介

> 原文：<https://dev.to/5anthosh/an-introduction-to-javascript-31nc>

这篇文章最初发表在我的博客上，在这里找到原文

让我们看看什么是 Javascript，我们可以用它实现什么，以及 Javascript 的局限性。

[![JavaScript](img/e7de51fe27cb0473f8a0c01470293c24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uw-s0jfE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://msatechnosoft.in/blog/wp-content/uploads/2017/04/JavaScript-in-web-design-Basics-Advantages-Limitations-Form-Validation-MSA-Technosoft.png)

## JavaScript 是什么？

JavaScript 是世界上最流行的编程语言之一。作为第一种也是唯一一种由 web 浏览器本地支持的脚本语言，它简单地被卡住了

> Javascript 让网页活了起来

在 Javascript 出现之前，每个网站都提供静态内容，不会让网页看起来活灵活现。Javascript 允许实现复杂的事情，如显示时间、验证 web 表单、交互式地图、2D/3D 图形。

Javascript 是一种脚本语言，因此脚本作为纯文本执行。它们不需要编译就能执行。它们可以写在网页中，并在网页浏览器加载网页时执行。

> Java 和 Javascript 是非常不同的编程语言。

JavaScript 最初被命名为 Mocha，后来它被重命名为 LiveScript，然后是 JavaScript。Javascript 创建的时候，Java 在当时非常流行，所以他们决定将其重命名为 Javascript 会对其有所帮助。

## JavaScript 引擎

[![V8](img/9acc5ca45ee74279bf0b2993dbcbfbca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J_vQaBJR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttp%253A%252F%252Fi1-news.softpedia-static.com%252Fimages%252Fnews-700%252FChrome-May-Add-Support-for-Mozilla-s-Asm-js-the-Native-Client-Competitor.png%26f%3D1)

Javascript 引擎是在浏览器中执行 Javascript 的虚拟机。Javascript 可以在任何装有 Javascript 引擎的设备上运行。Javascript 将脚本转换成机器语言。机器代码运行速度非常快。

每个浏览器都有自己的 Javascript 引擎

| Javascript 引擎 | 浏览器 |
| --- | --- |
| [V8](https://v8.dev/) | 铬和歌剧 |
| [蜘蛛猴](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey) | 火狐浏览器 |
| [脉轮核心](https://en.wikipedia.org/wiki/Chakra_(JavaScript_engine)) | 微软新型浏览器 |

SpiderMonkey 是第一个 JavaScript 引擎，由 Brendan Eich(JavaScript 的创造者)编写

[Node.js](https://en.wikipedia.org/wiki/Node.js) 是基于 Chrome 的 V8 JavaScript 引擎构建的 JavaScript 运行时环境。Node.js 在服务器端执行 JavaScript 代码。

## JavaScript 能做什么？

Javascript 非常适合在浏览器中执行任务。它主要用于与用户进行交互。

*   它可以改变现有的 HTML 内容，修改 HTML 样式，HTML 属性。
*   它可以验证输入表单。
*   对用户动作做出反应，如鼠标点击、按键
*   执行计算。
*   向服务器发送请求，获取数据(AJAX)
*   在浏览器中获取和设置 cookies
*   将数据存储在浏览器本地存储中

Javascript 的能力取决于它的环境。Nodejs 允许 Javascript 访问文件系统，访问网络，调用[系统调用](https://en.wikipedia.org/wiki/System_call)

Javascript 与 HTML/CSS 完全集成，但是网页上的 Javascript 不能访问硬盘上的文件。