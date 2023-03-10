# 为什么我的 Web 组件继承样式？

> 原文：<https://dev.to/lamplightdev/why-is-my-web-component-inheriting-styles-of3>

Web 组件中 Shadow DOM 的一个重要特性是样式被封装到组件中——您可以设计组件的样式，而不用担心任何说明符(`id`、`class`等)。)冲突或样式“泄露”到页面上的其他元素。

这通常会导致相反的想法——组件之外的样式不会越过阴影边界并“渗入”到组件中。然而，这只是部分正确。

虽然*说明符*不会泄漏到您的组件中(例如，应用到组件外部的样式规则中的`p`元素的颜色不会影响您的阴影 DOM 中的任何`p`元素，尽管该规则将应用到您的光线 DOM 或开槽内容)，但应用到包含您的组件的任何元素的*可继承样式*将同时应用到您的阴影和光线 DOM。

### 可继承的风格

可继承样式是在父元素上定义的样式，然后由子元素继承。最明显的可继承样式是那些定义在`html`和`body`标签上的样式。以`color`为例，如果在`body`(页面上每个元素的容器)上有一个`color: green`集合，它将被页面上的所有元素继承，包括 Web 组件，除非被另一个更具体的规则覆盖。

可继承的样式是必要的，所以我们不需要，例如，在每个元素上写一个`color`规则。但是并不是所有的样式都是可继承的，否则一个元素上的`border`样式会被它的所有子元素继承:

[https://glitch.com/embed/#!/embed/uttermost-leaf?path=index.html](https://glitch.com/embed/#!/embed/uttermost-leaf?path=index.html)

阴影 DOM 中设置的规则以同样的方式起作用——可继承的样式应用于所有的子对象(阴影和光线 DOM ),因此向阴影 DOM 添加一个`color: red`:

[https://glitch.com/embed/#!/embed/sprout-caraway?path=index.html](https://glitch.com/embed/#!/embed/sprout-caraway?path=index.html)

### 被选中的人

那么哪些风格是可以继承的，哪些不是呢？这些都是由 CSS 规范定义的。最常见的有:

*   `color`
*   `cursor`
*   `direction`
*   `font`
*   `font-*` ( `font-family`，`font-size`等。)
*   `letter-spacing`
*   `line-height`
*   `text-align`
*   `text-indent`
*   `text-transform`
*   `visibility`
*   `word-spacing`

这就是为什么这些样式*会*泄露到你的 Web 组件中。

### 如果我不想让我的组件继承它的父组件怎么办？

幸运的是，您可以使用`all: initial`重置组件的所有可继承样式(阴影和光线 DOM ),这将样式重置回 CSS 默认值或浏览器“用户代理样式表”中定义的样式。请注意，在 Light DOM 中匹配的外部说明符不会被重置。

[https://glitch.com/embed/#!/embed/sticky-tartan?path=index.html](https://glitch.com/embed/#!/embed/sticky-tartan?path=index.html)

### 如果我想从外部设计我的组件，该怎么办？

这就是 CSS 自定义属性以及新的`::part`和`::theme`伪元素的用武之地，我将在接下来的文章中介绍它们。

如果你有任何反馈或问题，我很乐意倾听。

[订阅邮件列表以获得关于 Web 组件和构建高性能网站的新帖子的通知](http://eepurl.com/gvjFwv)