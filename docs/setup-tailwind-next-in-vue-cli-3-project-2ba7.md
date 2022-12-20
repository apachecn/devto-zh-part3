# 在 Vue CLI 3 项目中设置 Tailwind@next

> 原文：<https://dev.to/gokatz/setup-tailwind-next-in-vue-cli-3-project-2ba7>

设置 Tailwind 是一个非常简单的过程，只需要几个简单的步骤。但是，不熟悉 Webpack 或普通 CSS 配置(如 PostCSS)的开发人员(像我一样)可能会觉得很难将所有部分连接起来。这篇文章将有助于在 Vue CLI 3 项目中使用基本配置来设置和运行 tailwind。

## 创建新项目

使用 Vue CLI 3 使用您的任何预设创建一个新的 Vue 项目。

```
vue create my-app 
```

Enter fullscreen mode Exit fullscreen mode

## 安装顺风(@next)

```
# Using npm
npm install tailwindcss@next --save-dev

# Using Yarn
yarn add tailwindcss@next --dev 
```

Enter fullscreen mode Exit fullscreen mode

## 加载所有顺风默认值

在一个`.css`文件中加载顺风默认值。创建一个新的`css`文件(比如说`src/assets/css/tailwind.css`)并加载默认值

```
/* tailwind.css */

@tailwind preflight;
@tailwind components;
@tailwind utilities; 
```

Enter fullscreen mode Exit fullscreen mode

将此`css`文件导入到`main.js`条目文件中。

```
// main.js

// other imports
import '@/assets/css/tailwind.css' 
```

Enter fullscreen mode Exit fullscreen mode

## 配置 PostCSS

配置 PostCSS 使用顺风样式

```
// postcss.config.js

module.exports = {
  plugins: [
    require('tailwindcss'),
    require('autoprefixer'),
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在重启 vue 服务器并开始使用 Tailwind🎉观看本系列，了解更多与顺风和 Vue 相关的技巧😉