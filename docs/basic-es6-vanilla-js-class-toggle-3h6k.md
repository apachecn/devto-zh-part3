# 基本 ES6 普通 JS 类切换

> 原文：<https://dev.to/magnificode/basic-es6-vanilla-js-class-toggle-3h6k>

哇，在我发表了一篇关于普通 JS 的基本类切换的[lil’博客文章](https://dommagnifi.co/2016-05-16-basic-class-toggle-with-vanilla-js/)后，我想我可以写出如何用 ES6 做同样的事情。

自从我们上次在 2016 年访问我们的类切换任务以来，相当多的事情发生了变化。让我们从我们离开的地方开始，对它进行一点重构，使它更符合当前的标准。

```
var el = document.querySelector('.toggle-me');

el.onclick = function() {
  el.classList.toggle('active');
} 
```

啊是的。没有什么比看看你自己的旧代码更好的了...🤦‍.首先，让我们更新如何使用`const`缓存 DOM 元素。

```
const el = document.querySelector('.toggle-me');

el.onclick = function() {
  el.classList.toggle('active');
} 
```

这里的变化非常小，但是请记住，当使用`const`时，你要确保你所引用的东西不会发生进一步的变异。我们知道`.toggle-me` DOM 节点不会变异成完全不同的东西，所以`const`在这里是合适的。

接下来，我们来看看函数。目前我们使用`onclick`事件处理程序来检测用户何时点击我们定义的元素。现在这个开箱即用就可以了，但是让我们加入一个 ES6 箭头函数！

作为入门，箭头函数只是编写常规函数的一种更简洁的方式，就像我们在上面的代码片段中所做的那样。然而，值得注意的是`this`在箭头函数中的行为完全不同。泰勒·麦金尼斯有一篇[精彩的文章](https://tylermcginnis.com/arrow-functions/)，深入探讨了箭头函数的来龙去脉。如果你想潜得更深一点的话，可以随时到那边去。

出于我们的考虑，我们可以利用一个箭头函数来精简我们的代码:

```
const el = document.querySelector('.toggle-me');

el.onclick = () => el.classList.toggle('active'); 
```

不错！我们可以将其精简为两行非常简洁的 javascript 代码。使用箭头函数，我们隐式地在元素上运行切换方法。不需要在我们的单行函数周围加上花括号。这将完美地工作，并且如果类切换是一个`<button>`或`<input type="submit">`元素，它仍然是可访问的。`onclick`监听器还负责使用键盘导航的用户。`enter`和`space`键将触发对这些元素的点击。但是如果我们需要在一个`<div>`、`<span>`或者其他默认情况下不交互的元素上切换一个类呢？

## 类切换非交互元素

**这里是大 ol 的免责声明。如果你的页面上有一个元素，当你与它交互时，你会*几乎总是*使用按钮。这是说“嘿，当你点击这个东西的时候，你正在浏览的页面上会发生一些事情”的标准。但是，如果您完全不能使用按钮或提交输入，那么您必须让键盘用户能够访问该元素。**

对于像`<div>`和`<span>`这样的非交互元素，必须采取额外的步骤来确保交互对于键盘用户是可访问的。我们将在代码片段中添加一个新的事件监听器，确保所有人都可以使用。

```
const el = document.querySelector('.toggle-me');

el.onclick = () => el.classList.toggle('active');
el.addEventListener('keyup', (event) => {
  if(event.keyCode === 13 || event.keyCode === 32) {
    el.classList.toggle('active');
  }
}); 
```

也请不要忘记，因为这些元素在默认情况下不是交互式的，我们需要确保用户可以在 DOM 中正确地聚焦项目。让我们假设我们的元素是一个`<div>`。通过向该 div 添加一个`tabindex`属性，我们能够确保键盘用户能够聚焦该元素。

```
<div class="toggle-me" tabindex="0">Hi!</div> 
```

**这里也要注意，当使用`tabindex`时，你很少会想从文档的标签流中取出元素。使用`tabindex="0"`使元素可聚焦，但保持在正常的 tab 流中。除了 0 之外的任何值都会将其从流中去掉，并且可能会给键盘用户造成混乱。只有在绝对必要的情况下才这样做，并且你有一个计划来确保键盘用户确切地知道发生了什么**。

`keyup`事件监听器将监听键盘用户何时点击并释放一个键。在我们的 arrow 函数中，我们现在需要传递 keyup 事件，这样我们就可以捕获该事件，并且只有当 enter 或 space 键被点击时才触发类切换(这些是指示用户试图在我们的元素上执行操作的键盘键)。捕捉到事件后，我们就可以运行类切换了！

因为我们运行了两次切换，所以让我们再次合并。

```
const el = document.querySelector('.toggle-me');
const handleToggle = () => el.classList.toggle('active');

el.onclick = () => handleToggle();
el.addEventListener('keyup', (event) => {
  if(event.keyCode === 13 || event.keyCode === 32) {
    handleToggle();
  }
}); 
```

使用 ES6，我们可以将箭头函数分配给一个`const`，并在多个地方调用该函数。

壮观！至此，对于交互式和非交互式元素，您现在应该能够轻松地将 CSS 类切换提升到 ES6。