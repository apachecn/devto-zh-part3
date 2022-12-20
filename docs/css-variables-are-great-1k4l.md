# CSS 变量很棒！

> 原文：<https://dev.to/mornir/css-variables-are-great-1k4l>

### 声明一个新变量

您可以在样式表的任何地方定义 CSS 变量，但是您通常希望在文档的根元素中声明它们。通过这样做，您可以全局访问它们。

```
 :root {
    --main-color: blue;
} 
```

Enter fullscreen mode Exit fullscreen mode

一个新变量用两个破折号声明，可以用`var()` CSS 函数:
访问

```
 .main-header {
    background-color: var(--main-color);
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，来自 SASS，语法对您来说可能看起来很奇怪。需要一些时间来适应。

### 用 JavaScript 更新值

这就是 CSS 变量的真正优势:它们可以在运行时改变，不像 SASS 变量那样被编译成常规的 CSS。

下面是如何更新一个 CSS 变量:

```
document.documentElement.style.setProperty('--main-color', 'green') 
```

Enter fullscreen mode Exit fullscreen mode

现在，包含该变量的所有属性的值都将设置为“绿色”。

### 真实的例子

最近，我一直在开发一个小型的 Nuxt.js 应用程序，其中我需要在不同路径上的两个组件之间传递一个值(一个十六进制颜色代码)。十六进制颜色应该改变页眉和页脚的背景颜色。

由于路由的原因，使用事件发射器是不可能的，而且添加状态管理库感觉有点矫枉过正。

💡我在根元素上声明了一个 CSS 变量，并让组件更新它的值。因为另一个组件依赖于同一个变量，所以它的值也被更新。

GitHub 上具体行的链接:[https://GitHub . com/mornir/copy work-portfolio/blob/master/pages/_ slug . vue # L109](https://github.com/mornir/copywork-portfolio/blob/master/pages/_slug.vue#L109)

网址链接: [copywork.surge.sh](https://copywork.surge.sh)