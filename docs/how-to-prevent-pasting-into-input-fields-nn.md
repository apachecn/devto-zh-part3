# 如何防止粘贴到输入字段

> 原文：<https://dev.to/clairecodes/how-to-prevent-pasting-into-input-fields-nn>

我对另一个不允许我将密码粘贴到确认密码字段的 web 表单感到沮丧，想知道它是如何做到的，并决定写下它。我写这篇文章的目的是对你是否应该这样做保持公正，并鼓励你做出自己的决定。然而，我也强烈建议你**阅读这篇文章下面的讨论，因为它清楚地显示了这个特性是多么的多余和不必要。**

感谢 Dev 社区成员的所有评论和反馈，感谢他们让这个网站成为互联网如此积极的一部分！

* * *

在某些表单中，“确认电子邮件地址”或“确认密码”栏不允许用户向其中粘贴文本。这个想法是让用户键入他们的电子邮件或密码两次，以帮助捕捉他们在那些重要的“电子邮件”和“密码”值中可能犯的任何打字错误。

这个功能是如何实现的？如何阻止用户将内容粘贴到 HTML 输入字段中？

我们可以使用 JavaScript 来定位输入字段的[粘贴事件](https://developer.mozilla.org/en-US/docs/Web/Events/paste)并改变它的工作方式:

```
<input type="text" id="no-paste" />

<script>
  const pasteBox = document.getElementById("no-paste");
  pasteBox.onpaste = e => {
    e.preventDefault();
    return false;
  };
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这段代码取消了粘贴事件的默认行为(即将剪贴板的内容粘贴到 input 元素中)。当用户试图使用键盘快捷键或[上下文菜单](https://en.wikipedia.org/wiki/Context_menu)将内容粘贴到字段中时，什么都不会发生。

在下面的 CodePen 示例中尝试一下:

[https://codepen.io/claireparker/embed/XOmwqy?height=600&default-tab=result&embed-version=2](https://codepen.io/claireparker/embed/XOmwqy?height=600&default-tab=result&embed-version=2)

还有用于[剪切](https://developer.mozilla.org/en-US/docs/Web/Events/cut)和[复制](https://developer.mozilla.org/en-US/docs/Web/Events/copy)动作的事件。

现代浏览器对粘贴事件有很好的支持。这些事件是[剪贴板 API](https://www.w3.org/TR/clipboard-apis/#the-paste-action) 的一部分。剪贴板 API 还包括访问剪贴板的内容，它具有不同级别的支持。更多信息见剪贴板 API 的[can use 表。](https://caniuse.com/#feat=clipboard)

## 是否应该禁用粘贴功能？

现在你知道了如何改变你的网页中粘贴事件的预期行为，问题是你是否应该这样做。关于粘贴事件的这个 [StackOverflow 问题的答案阻止开发人员篡改默认的浏览器行为。发帖者反对改变预期的浏览器行为，因为这会让用户困惑。此外，如果用户决定冒着包含输入错误的风险将数据复制粘贴到表单域中，那么我们应该允许他们这样做。](https://stackoverflow.com/questions/1226574/disable-pasting-text-into-html-form/1226721)

每个网站都不一样，所以没有确定的答案。为了给用户提供最好的体验，在构建网站时考虑这样的问题是一个好主意。