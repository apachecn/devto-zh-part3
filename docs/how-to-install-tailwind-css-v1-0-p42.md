# 如何安装顺风 CSS v1.0 版

> 原文：<https://dev.to/justalever/how-to-install-tailwind-css-v1-0-p42>

最近，Tailwind CSS 已经成为我最喜欢的 CSS 框架。该框架的重点是实用优先，这意味着向 HTML 元素添加类来统一样式。本指南将教你如何安装即将推出的顺风 CSS 的[新测试版 1.0](https://github.com/tailwindcss/tailwindcss/releases/tag/v1.0.0-beta.3) 。

这篇文章也是一个新系列的开始，名为《让我们建立:用顺风 CSS》。在这个系列中，我将专门使用 Tailwind CSS 创建 UI 组件和项目。我们将利用这个框架，并可能扩展它，从头开始创建令人敬畏的 UI。我们甚至可以克隆其他流行的 UI 作为用例。

### 为什么要顺风 CSS？

起初我讨厌纯粹的功能/原子/实用 CSS 的概念，但是现在已经喜欢上它了。我最初讨厌它，因为在我的 HTML 中写这么多的类名感觉很混乱。感觉好像我几乎在写内嵌样式(你有点……)。

我在这些要点中发现了顺风 CSS 方法的妙处:

*   不那么固执己见(顺风有它的默认值，但你可以自定义一切)
*   可配置的
*   几乎不需要在你的堆栈中寻找 CSS
*   几乎不需要在堆栈中重复编写 CSS。
*   缩放只是更容易
*   原型只是更容易
*   减少标记和 CSS 文件之间的跳转

[https://www.youtube.com/embed/YqHQtTFj74w](https://www.youtube.com/embed/YqHQtTFj74w)

### 安装

安装顺风相当容易。您将需要某种编译过程来编译代码。它利用了 PostCSS，所以像 Gulp、Wepback、Laravel Mix 或*插入您最喜欢的构建工具名称*就足够了。

在本指南中，我将使用 Adam Wathan 的 [webpacker starter kit](https://github.com/tailwindcss/webpack-starter) 用于顺风。我会修改它以适应新的测试版。

#### 开始做事——选项 1

*(如果您正在从旧版本迁移)*

*   下载初学者工具包并运行`yarn`。
*   将`tailwind.js`文件重命名为`tailwind.config.js`
*   在`postcss.config.js`内更改以下内容:

```
module.exports = {
  plugins: [require('tailwindcss')('./tailwind.js')],
} 
```

致:

```
module.exports = {
  plugins: [require('tailwindcss')('./tailwind.config.js')],
} 
```

*   用[新的默认](https://github.com/tailwindcss/tailwindcss/blob/next/stubs/defaultConfig.stub.js)替换`tailwind.config.js`的内容。
*   在`tailwind.css`内用`@import base;`替换`@import preflight;`线
*   运行`yarn dev`

#### 开始做事——选项 2

*(如果您是该框架的新手，建议使用)*

*   下载[初学者工具包](https://github.com/tailwindcss/webpack-starter)并运行`yarn`。
*   将新的顺风测试版`yarn add -D tailwindcss@next`添加到您的项目中。
*   init Tailwind–`yarn tailwind init`–这将在您的项目中创建一个新的`tailwind.config.js`文件。
*   在`postcss.config.js`内更改以下内容:

```
module.exports = {
  plugins: [require('tailwindcss')('./tailwind.js')],
} 
```

致:

```
module.exports = {
  plugins: [require('tailwindcss')('./tailwind.config.js')],
} 
```

*   在`tailwind.css`内用`@import base;`替换`@import preflight;`线
*   运行`yarn dev`

### 接下来是什么？

我很想听听你对用 Tailwind CSS 构建的组件/项目/设计的看法。我的目标是从小处着手，然后逐步扩大。我们将从组件级别开始，然后逐步扩展到更大的页面/视图。从那里，我们可以扩展 Tailwind，并了解由于插件和可选配置，该框架还可以做什么。我还将向您展示如何在您最喜欢的框架上安装它。

### 无耻的塞时间

如果你喜欢这篇文章，我在 [YouTube](https://youtube.com/c/webcrunch) 和我的[博客](https://web-crunch.com)上有更多视频。希望收件箱中有更多这样的内容吗？[订阅我的简讯](https://web-crunch.com/subcribe)自动获取。

#### 查看我的课程

[![https://i2.wp.com/i.imgur.com/KIaaJEB.jpg?ssl=1](img/083250001f26c8017cdaafe107a730f9.png)](https://hellorails.io)

想从头开始学习 Ruby on Rails 吗？查看我即将推出的课程 [Hello Rails](https://hellorails.io) 。我在整个课程中使用了 Tailwind CSS。

在推特上关注 [@hello_rails](https://twitter.com/hello_rails) 和[本人@justalever](https://twitter.com/justalever) 。

帖子[如何安装顺风 CSS v1.0](https://web-crunch.com/how-to-install-tailwind-css/) 最早出现在 [Web-Crunch](https://web-crunch.com) 上。