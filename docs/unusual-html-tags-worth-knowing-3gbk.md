# 值得了解🧩的不寻常的 HTML 标签

> 原文：<https://dev.to/christopherkade/unusual-html-tags-worth-knowing-3gbk>

使用正确的 HTML 元素比我们许多人意识到的更重要。

语义 HTML，不仅仅是表现，而是给页面带来意义。一个`<p>`元素包含一个段落，每个人都知道它应该是什么样子。它不仅有助于向搜索引擎传达页面内容，还有助于屏幕阅读器尽可能高效地完成工作。

本文将列出一些鲜为人知的标签，让你在日常生活中拥有尽可能多的工具。

## `<abbr>`:缩写

应包含缩写，如下例所示。您可以用它来定义读者不熟悉的缩写，使用 title 属性或提供定义的内联文本。

[https://codepen.io/christopherkade/embed/EzpMLZ?height=600&default-tab=html,result&embed-version=2](https://codepen.io/christopherkade/embed/EzpMLZ?height=600&default-tab=html,result&embed-version=2)

[文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/abbr)

## `<dfn>`:定义

你应该在句子上下文中描述的元素上使用这个元素。祖先`<p>`、`<dt>` / `<dd>`或`<section>`被认为是那个词的定义。

[https://codepen.io/christopherkade/embed/WBKmaV?height=600&default-tab=html,result&embed-version=2](https://codepen.io/christopherkade/embed/WBKmaV?height=600&default-tab=html,result&embed-version=2)

[文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dfn)

## `<address>`:联系方式

你可以用它来提供联系信息。

正如 Lars De Richter 在评论中所分享的，我们应该记住，并非所有类型的地址都应该使用此元素显示:

> address 提供的信息可能包括文档维护者的姓名、维护者网页的链接、反馈的电子邮件地址、邮政地址、电话号码等等。address 元素不适用于所有邮政地址和电子邮件地址；它应该保留用于提供有关文档联系人的此类信息。

[https://codepen.io/christopherkade/embed/EzpMBX?height=600&default-tab=html,result&embed-version=2](https://codepen.io/christopherkade/embed/EzpMBX?height=600&default-tab=html,result&embed-version=2)

[文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/address)

## `<cite>`:引文

引用创造性作品时应使用。

[https://codepen.io/christopherkade/embed/QRBPGN?height=600&default-tab=html,result&embed-version=2](https://codepen.io/christopherkade/embed/QRBPGN?height=600&default-tab=html,result&embed-version=2)

[文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/cite)

## `<del>`:删除文字

该标签可以包含从文档中删除的文本。

这个标签提出的[可访问性问题](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/del#Accessibility_concerns)绝对值得注意。

[https://codepen.io/christopherkade/embed/pmZBeN?height=600&default-tab=html,result&embed-version=2](https://codepen.io/christopherkade/embed/pmZBeN?height=600&default-tab=html,result&embed-version=2)

[文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/del)

## `<hgroup>`:多级标题

这个标签可以重组一个文档中的多个标题(`<h1>-<h6>`)。这样，这个组中的第二个孩子将不会创建他们自己的单独部分。

[https://codepen.io/christopherkade/embed/mYjgMZ?height=600&default-tab=html,result&embed-version=2](https://codepen.io/christopherkade/embed/mYjgMZ?height=600&default-tab=html,result&embed-version=2)

值得注意的是,“元素已经从 HTML5 (W3C)规范中删除，但它仍然存在于 HTML 的 WHATWG 版本中。不过，大多数浏览器都部分实现了这一功能，所以不太可能消失。”所以要小心使用😄[更多信息](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/hgroup#Usage_notes)

[文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/hgroup)

## `<ins>`:插入文字

表示添加到文档中的一系列文本，非常类似于 Github 显示的一行被替换的代码。

[https://codepen.io/christopherkade/embed/gJjyjb?height=600&default-tab=html,result&embed-version=2](https://codepen.io/christopherkade/embed/gJjyjb?height=600&default-tab=html,result&embed-version=2)

[文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ins)

# 包装完毕

有如此多的 HTML 标签是绝大多数 web 开发人员所不知道的。将内容和意义放在同一个层面上，将有助于我们构建更好、更具包容性的网页。所需要的只是练习😄

我希望您能找到这些标签的一些用例，尽可能具体一些。

不管怎样，非常感谢你的阅读，如果你喜欢这篇文章并学到了一些东西，在 Twitter 上关注我 [@christo_kade](https://twitter.com/christo_kade) 将是了解我最新文章&的最佳方式。