# 模板标签中的模板文字表达式似乎错过了一个机会

> 原文：<https://dev.to/davecranwell/template-literals-within-the-template-tag-seem-like-a-missed-opportunity-1deh>

模板文字很神奇，但我最近发现它们让一切看起来像谚语中的钉子，特别是在动态更新大块 HTML 时。

我仍然在一个大部分是后端的环境中工作，Perl 从模板中生成 HTML，在此基础上我们添加逐渐增强的 javascript 组件。

当您的后端和前端都需要能够生成基于模式库的通用 UI 模式时，不得不在多个地方为每个 UI 模式定义 HTML 源代码(一次在 Perl 中，一次在 JS 中)会令人沮丧。

标签可能会有用。后端可以生成组件所需的 HTML(例如待办事项列表中的每个待办事项)，JS 可以使用该 HTML 模板来生成每个新的待办事项。

但是据我所知，将值动态插入模板的唯一方法是在模板内容中极其笨拙地使用 CSS 选择器。(参见:[https://blog . teamtreehouse . com/creating-reusable-markup-with-the-html-template-element](https://blog.teamtreehouse.com/creating-reusable-markup-with-the-html-template-element))

现在模板文字出现在每个主流浏览器中，在`<template>` API 中增加对表达式的支持不是更有意义吗？例如

```
<template id="test-template">
    hello ${where}
</template> 
```

Enter fullscreen mode Exit fullscreen mode

```
const tmpl = document.getElementById('test-template');

// where "MAGIC" is some means of supplying data as json 
document.body.appendChild(tmpl.content.cloneNode(true, MAGIC)); 
```

Enter fullscreen mode Exit fullscreen mode

Web 组件拥有`<slot>`系统，但这需要采用更大的技术。而且，是的，总是有小胡子/车把等，但这又增加了 JS 的有效载荷。是的，我们也使用 React，但是升级所有的东西来反应并不总是可能的。

难道只有我一个人在努力实现这个目标吗？有人为此找到了一个无框架或框架精简的解决方案吗？