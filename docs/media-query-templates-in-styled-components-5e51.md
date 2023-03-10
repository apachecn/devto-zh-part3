# 样式化组件中的媒体查询模板

> 原文：<https://dev.to/ghost/media-query-templates-in-styled-components-5e51>

最近，在 React 生态系统中，组织项目中样式表的“JS 中的 CSS”方法变得越来越流行。我认为这主要有两个原因:

*   在一个一切都是组件的世界里，将样式与视图代码结合起来使得每个组件更易于管理，并且在项目之间更易于共享。
*   使用**变量**的能力，特别是通过用 Javascript 模板文字对它们进行插值，比使用像 SASS 或 LESS 这样的 CSS 预处理程序更灵活。

也就是说，大多数人似乎更喜欢在 JS 中实现 CSS 的两种方法之一:[样式化组件](https://www.styled-components.com/)和[情感](https://emotion.sh/)。两者在许多方面都很相似，但是我的大部分经验都是在两个项目中较大的一个上，即样式组件。

在这篇文章中，我将谈到我认为如何使用样式化组件的最巧妙的部分:[媒体模板](https://www.styled-components.com/docs/advanced#media-templates)！下面是样式组件文档对此的评论:

> 由于媒体查询很长，并且经常在整个应用程序中重复，因此为它们创建一个模板会很有用。由于 JavaScript 的功能特性，您可以轻松地定义自己的标记模板文字来包装媒体查询中的样式。

让我向你展示我如何在我的网站中使用这种方法: