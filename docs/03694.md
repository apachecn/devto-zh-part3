# 在不使用任何插件的情况下用 Gridsome 设置 Tailwind 和 Purgecss

> 原文：<https://dev.to/drewtownchi/setting-up-tailwind-and-purgecss-with-gridsome-without-using-any-plugins-5666>

在 [drewtown.dev](https://www.drewtown.dev) 上查看更多帖子

如果你现在还没有听说过 Tailwind，那么它是实用优先的 CSS 框架领域的一颗冉冉升起的新星，它使得使用 CSS 进行开发变得有趣而高效。有了 Tailwind，你就不再与框架对抗，不再试图让盒子轮廓变蓝，或者让你的卡片之间的空白区域达到某个`rem`大小。

相反，Tailwind 提供了一些工具和实用程序，通过将必要的部分组合在一起来构建外观独特的网站。仍然有机会创建预定义的`.button`或`.box`组件。但是，Tailwind 并没有提供现成的组件，而是让您可以灵活地决定那些已定义的组件应该是什么，以及您需要多少组件。

当使用基于实用程序的 CSS 库时，您总是要选择库的一部分来放入您的项目中。将`my-8 shadow-lg rounded-sm`添加到 div 意味着您的项目现在依赖于这些类。但是`my-0 and my-2`和所有其他顺风的种类呢？如果不使用 Purgecss 之类的东西，您的已部署站点将需要把 Tailwind 提供的所有未使用的类带入生产环境。这是次优的，因为客户端现在需要服务器发送这些字节，而每个客户端都将下载它们。

尽管 Tailwind 提供了许多不同的填充、边距、颜色等选项，但你很可能只会用到其中的一小部分。使用 Purgecss，我们可以通过扫描文件中那些类名的实例并删除那些在任何模板中未使用的实例来减少膨胀。

在写这篇文章的时候，所有的 drewtown.dev 的样式都是 3.6KB 的 gzipped，同时在开发的时候仍然可以使用所有的 Tailwind。结合顺风和栅格是显而易见的。静态 JAM 堆栈的速度需要高效的 CSS 堆栈与之配合，而 Tailwind 就是答案。

# 依赖关系

```
npm i -S @fullhuman/postcss-purgecss tailwindcss 
```

Enter fullscreen mode Exit fullscreen mode

# 创建顺风配置文件

Tailwind 需要一个[配置文件](https://tailwindcss.com/docs/configuration)，它将用于生成必要的类。我们需要初始化一个，默认情况下它会创建一个`tailwind.js`文件。

```
npx tailwind init [optionally provide a file name here] 
```

Enter fullscreen mode Exit fullscreen mode

# 导入顺风

一旦初始化过程完成，我们必须导入到我们的项目顺风。这可以在您的`App.vue`文件中完成，但我更喜欢创建一个`global.css`文件。在 global.css 文件中，我可以在 Gridsome 项目范围之外创建任何必要的全局组件。

```
/* global.css */
@tailwind base;
@tailwind components;
/* Add your btn/input/etc components here */
@tailwind utilities; 
```

Enter fullscreen mode Exit fullscreen mode

将 global.css 文件导入到 main.js 文件中。

```
//main.js
import "./styles/global.css"; 
```

Enter fullscreen mode Exit fullscreen mode

# 网格体配置

Gridsome 提供了 [CSS 加载器选项](https://gridsome.org/docs/config/#cssloaderoptions)，这使得我们可以在没有插件的情况下挂钩到构建过程中。

首先，我们必须导入 Tailwind，并告诉它在哪里可以找到 Tailwind 配置文件。接下来，导入 Purgecss 的 Postcss 插件。当我们在开发的时候，运行 Purgecss 是很费时间的，也是不必要的。通过检查生产中的`process.env.NODE_ENV`的值，可以有条件地将 purgecss 插件添加到 postcss 插件数组中，而不是一直运行它。

```
const tailwindcss = require("tailwindcss");
const purgecss = require("@fullhuman/postcss-purgecss");

module.exports = {
  siteName: "The Site Name",
  plugins: [],
  css: {
    loaderOptions: {
      postcss: {
        plugins: [
          tailwindcss,
          ...process.env.NODE_ENV === "production" ? [purgecss] : []
        ],
      },
    },
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

# Purgecss 配置

最后，我们需要对 Purgecss 做一些修改。

```
module.exports = {
  content: [
    "./src/**/*.vue",
    "./src/**/*.js",
    "./src/**/*.jsx",
    "./src/**/*.html",
    "./src/**/*.pug",
    "./src/**/*.md",
  ],
  whitelist: ["svg:not(:root).svg-inline--fa"], 
  whitelistPatterns: [/^fa-/, /^svg-inline--fa/], 
  whitelistPatternsChildren: [/^token/, /^pre/, /^code/], 
  defaultExtractor: content => content.match(/[A-Za-z0-9-_:/]+/g) || [] 
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 内容

内容数组允许您指定一系列路径来检查 CSS 类。你可以根据需要包容或限制。

## 提取器

需要为 Purgecss 提供一个`defaultExtractor`来正确解析 Tailwind 类。如果不添加提取器，Purgecss 将会丢失模板中许多包含破折号或冒号的类。

## 白名单

有时 Purgecss 在从 css 文件中删除类时可能有点过于激进。通常情况下，Purgecss 无法访问或在运行时加载的动态类或第三方库会出现这种情况。您有责任将它们列入 Purgecss 中可用的[白名单选项](https://www.purgecss.com/whitelisting)中的白名单，无论它是在配置文件中(如上例),还是通过 css 文件中的注释。

## 包装完毕

此时，Tailwind 被加载到 Gridsome 项目中，所有许多帮助实用程序都应该可用，使设计页面变得轻而易举。