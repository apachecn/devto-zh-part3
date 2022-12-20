# 盖茨比主题简介

> 原文：<https://dev.to/theianjones/a-brief-introduction-to-gatsby-themes-11b1>

# 什么是盖茨比主题

gatsby 主题不仅仅是 Gatsby 网站的视觉绘画，也不仅仅是像 wordpress 或 hugo 主题那样的预设设计。

主题允许你以可共享和可扩展的方式打包任何 gatsby 站点的功能。

> 主题是 NPM 可安装的盖茨比网站

## 为什么你会使用盖茨比主题

盖茨比主题可以是许多不同的东西:

*   预设、可配置`gatsby-config`
*   用户界面主题(暗模式与亮模式)
*   数据源(来自 contentful、json 文件等)
*   组件库

您可以根据需要在一个 gatsby 站点中安装任意多的主题。当您开发主题时，您也可以在 gatsby 配置中使用主题。主题允许你很容易地将你的代码分成不同的部分。

## 如何使用主题(docz)

生成一个新站点并安装`gatsby-theme-docz`。

```
npx gatsby new docz-theme-example
cd docz-theme-example && yarn add gatsby-theme-docz 
```

将主题添加到您的 gatsby 配置:

```
// gatsby-config.js
module.exports = {
  __experimentalThemes: ['gatsby-theme-docz'],
} 
```

转到你的`src/pages`目录，添加一个 [`mdx`](https://github.com/mdx-js/mdx) 文件。

```
// src/pages/helloworld.mdx
# Hello from MDX! 
```

现在，你可以启动你的盖茨比网站了:

```
gatsby develop 
```

现在访问 localhost:8000/helloworld 或任何您称之为新文件的名称。docz 主题将为项目中的每个`.mdx`文件生成一个页面。

因为您在一个 mdx 文件中，所以您可以导入任何 React 组件并使用它

```
// src/pages/helloworld.mdx
import Layout from '../Components/Layout'

export default ({children}) => <Layout>{children}</Layout> 
```

现在你会看到默认的`<Layout/>`包装了你的`mdx`文件。

## 更多资源

*   [盖茨比主题的官方介绍](https://www.gatsbyjs.org/blog/2018-11-11-introducing-gatsby-themes/)
*   [克里斯·比斯卡尔迪的主题示例](https://github.com/ChristopherBiscardi/gatsby-theme-examples)
*   凯尔·吉尔的《盖茨比主题简单指南》
*   [MDX 文档](https://github.com/mdx-js/mdx)
*   [盖茨比-主题-文档](https://github.com/pedronauck/docz/tree/master/core/gatsby-theme-docz)