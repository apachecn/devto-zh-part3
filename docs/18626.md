# README.md 作为个人网站

> 原文：<https://dev.to/snesjhon/readmemd-as-a-personal-site-1km7>

读完这篇文章[我的个人网站的演变](https://dev.to/dstarner/evolution-of-my-personal-site-1849)后，我回忆起我也经常更换我的个人网站。大多数时候，我会采用一个新的框架和新的设计。但我一直不满意。

我发现，当我建立网站、选择主题、设计工作流程(更不用说争论所有的依赖关系)时，我已经对如何持续更新这个网站不感兴趣了。

> 我想要更简单的东西。

以下是我一直在寻找的要求:

*   **最小设置**

    *   我不想处理组件，路由器，样板文件。
    *   本地开发工作流很好，但这取决于你多久会更新这个网站。对我来说，不是很频繁。
*   **干净/简单的主题**

    *   我喜欢修补主题，但我只是想操纵几个简单的 css 规则，然后上路。
*   易于在任何地方发布。

    *   只需推送发布即可。在我的本地或电话上。

最近我和盖茨比一起浏览了个人网站兔子洞。尽管这是一个很棒的包——同样，当我设置好所有东西的时候，我已经放弃了与网站保持同步。

有时候太多的选择并不是每个人都想要的。

* * *

# 灵感

在阅读 [dev.to](https://github.com/thepracticaldev/dev.to) README.md 的时候，我被写这篇文章时的愉悦所震撼。

它不仅清晰，而且传达了足够的信息让开发人员开始工作。

所以我想一定有办法把一个简单的 README.md 文件转换成一个成熟的网站，并把它作为我的个人网站。一个警告-我希望我在减价时看到的是网站。不是另一个主题或风格，只是 Github 风味的降价风格。

Welp。没那么简单。

* * *

# Github 页面，Gitlab 页面+对决. js

以下是我目前尝试将减价网站转换为个人网站时的选择。(免责声明:我并不是说这些服务不好——它们只是不适合我的用例)

github pages

*   正如这里详细介绍的，你可以上传一个 markdown 文件，并把它作为 Github 页面。

*   然而，Github 页面与 Jekyll 主题紧密相关。他们还添加了不属于 markdown 文件的标题和页眉。我不得不使用 css 来隐藏 jekyll 主题中不想要的部分。

**Gitlab Pages +对决. js**

*   Gitlab Pages 是另一种选择，因为它们有一个提交&推送管道运行器。这很好，因为我可以利用任何 markdown cli 转换器来发布 index.html。
*   在搜索了[许多](https://github.com/showdownjs/showdown)、[许多](https://pandoc.org/getting-started.html)转换器后，我发现当你想输出 html 时它们很棒——但不是一个完整的`index.html`。这是有意义的，因为它们是为输出到周围的框架而构建的。

### 那么为什么不只是一个简单的`html`文件呢？

正如我从@dstarner 博客中了解到的，有时最好保持简单。我写了一个简单的 index.html 文件，添加了 GFM css，就完成了！

但是，你试过在手机上写 html 吗？这不是最愉快的经历。为什么我不能只写 markdown？

* * *

# (完)工程一解决方案

我完全相信 96.4%的 npm 模块都是因为有人不能解决他们的问题，所以他们自己写的。

所以！我写了一个小 cli 来解决我的问题。

我把它叫做 [singlemd](https://gitlab.com/snesjhon/singlemd) ，它完全实现了我想要的。

*   这里有一个[演示站点](https://snesjhon.gitlab.io/singlemd/)，和 [README.md](https://gitlab.com/snesjhon/singlemd/blob/master/README.md) 源码。

我的网站现在只是一个简单的降价文件，我可以在任何地方修改。通过 Gitlab Pages 管道，我可以添加一个`.gitlab-ci.yml`

```
image: node:8.12.0 

pages:
  cache:
    paths:
    - node_modules/

  script:
  - npm install singlemd -g
  - singlemd --input ./README.md --output index.html --style ./public/style.css --title "snesjhon | Jhon Paredes"
  - mv index.html ./public
  artifacts:
    paths:
    - public
  only:
    - master 
```

Enter fullscreen mode Exit fullscreen mode

每当我更新 README.md 时，我的网站都会更新。**好玩！**

*   这是我的[个人网站](https://snesjhon.com/)和[回购](https://gitlab.com/snesjhon/snesjhon.gitlab.io)

### 教训

这是我发布的第一个 npm 包，我很高兴它是我正在积极利用的东西。还有很多工作要做，但总的来说，学习如何创建和发布 cli 包是一次很好的经历。

感谢您的阅读！