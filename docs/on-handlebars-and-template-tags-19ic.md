# 在把手和模板标签上

> 原文：<https://dev.to/trezy/on-handlebars-and-template-tags-19ic>

**本文最初发表于 2014 年 6 月 17 日的 [Codepen.io](https://codepen.io/trezy/post/on-handlebars-and-template-tags) 。**

[车把](http://handlebarsjs.com/)就是**牛逼**。我能把我的模板逻辑和我的应用程序逻辑分开吗？是的，请吧。然而，当我试图在我的本地编辑器中使用手柄时，它吐遍了那些难看的`<script>`标签。幸运的是，有一个令人敬畏的、未来友好的、符合规范的解决方案——`<template>`标签。

标签在很多方面都非常酷。来自 [HTML5 的石头文章](http://www.html5rocks.com/en/tutorials/webcomponents/template/)，下面是柱子的`<template>`标签:

1.  它的内容是有效的惰性，直到被激活。本质上，您的标记是隐藏的 DOM，并且不呈现。
2.  模板中的任何内容都不会有副作用。脚本不运行，图像不加载，音频不播放…直到模板被使用。
3.  内容被认为不在文档中。在主页中使用`document.getElementById()`或`querySelector()`不会返回模板的子节点。
4.  模板可以放在`<head>`、`<body>`或`<frameset>`中的任何地方，并且可以包含这些元素中允许的任何类型的内容。注意“anywhere”意味着`<template>`可以安全地用在 HTML 解析器不允许的地方……除了内容模型子元素。也可以放在`<table>`或者`<select>`的孩子:

```
<table>
  <tr>
    <template id=”cells-to-repeat”>
       <td></td>
    </template>
  </tr>
</table> 
```

Enter fullscreen mode Exit fullscreen mode

## 这个为什么没有用在别的地方？

这是一个很好的问题。我只能推测，但我认为这与最近/缺乏支持有关。大多数浏览器都支持`<template>`，尽管[我能使用](http://caniuse.com/#search=template)告诉我们，我们在 Internet Explorer(大惊喜)、Safari 7 和黑莓中缺乏支持。幸运的是，如果我们需要让它在任何地方都能工作，我们可以添加[这个垫片](http://jsfiddle.net/brianblakely/h3EmY/)。你可以在 HTML5 Rocks 的教程[中阅读更多关于`<template>`标签以及如何使用它的内容。](http://www.html5rocks.com/en/tutorials/webcomponents/template/)

不过，最酷的事情是——现在的车把用户实际上不得不写更少的代码。查看:

```
<!-- Handlebars recommended template block syntax -->
<script type=”text/x-handlebars-template” id=”article-template”>
  <article>
    <h1>{{title}}</h1>
    {{content}}
  </article> </script> 
```

Enter fullscreen mode Exit fullscreen mode

…只要把`<script>`标签换成`<template>`标签:

```
<!-- The exact same thing using template tags -->
<template id=”articleTemplate”>
  <article>
    <h1>{{title}}</h1>
    {{content}}
  </article>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

还有 blammo，你用的是超赞的新 HTML5 技术。你的编辑和我会因此而爱你的。😉

## 军规

正如 joao 在[笔会评论](https://codepen.io/trezy/post/on-handlebars-and-template-tags#post-comments)中指出的，并非一切都很好。看看这段代码:

```
<template id=”tableTemplate”>
  <table>
    {{#each}}
      <tr>
        <td>{{content}}</td>
      </tr>
    {{/each}}
  </table>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

看起来合法，对吧？不幸的是，它在浏览器中坏了。解析器仍然要求`<template>`标签中的代码是有效的 HTML，这意味着除非在单元格(`<td>`)中也有内容，否则在表格中不能有任何内容。但是，您可以在模板标签中创建表格单元格/行，并在表格中使用它们:

```
<table></table>

<template id=”tableTemplate”>
  {{#each}}
    <tr>
      <td>{{content}}</td>
    </tr>
  {{/each}}
</template> 
```

Enter fullscreen mode Exit fullscreen mode

它并不理想，我当然更喜欢一个更好的 DOM API。不过，这东西超级酷，我会到处用它。如果你利用它，让我知道！我将留给您我对`<template>`标签和把手的实现:

[https://codepen.io/trezy/embed/yxqCJ?height=600&default-tab=result&embed-version=2](https://codepen.io/trezy/embed/yxqCJ?height=600&default-tab=result&embed-version=2)