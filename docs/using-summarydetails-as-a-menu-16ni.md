# 将摘要/详细信息用作菜单

> 原文：<https://dev.to/marcush/using-summarydetails-as-a-menu-16ni>

我在 [#accessibleapp 项目](https://accessible-app.com)的下一篇文章中的初衷是关注“通知用户更改”，特别是关于不会触发整页重新加载的内容重新加载(单页应用程序中缺少整页重新加载导致必须提出路由策略)。在电子商务环境中，将商品放入购物车而不重定向到正确的购物车页面可能是这种内容的异步改变，并且可能难以被例如屏幕阅读器用户注意到。[莱昂妮·沃森写下了这个问题](https://tink.uk/accessible-forms-with-aria-live-regions/):

> 更新很容易一目了然，但对于屏幕阅读器来说一点也不容易。首先你必须发现一些事情已经改变，然后你必须找出它发生在哪里。即使你知道这一点，你仍然需要在摘要和产品信息之间来回移动焦点，每次你添加一个项目到购物篮。

与此同时，她指出了一个解决策略:使用 [aria-live regions](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions) 来宣布网站或应用程序中发生的变化，而无需正确的页面重新加载。所以，我想，我的可访问示例应用“ [Accessibooks](https://vuejs.accessible-app.com) ”的下一个功能是购物车功能——这毕竟是一个假的购物应用。在构建这个特性的过程中，我接触到了其他的可访问性主题，每一个都值得在自己的博客上发表:

*   动画，以及如何通过应用程序或操作系统中的设置关闭动画
*   购物车更改后 aria-live 区域的上述使用
*   以及如何将购物车标记为菜单小部件

本文接下来的部分将讨论后者。

### 明细/汇总

当你阅读[我的上一篇#accessibleapp 文章](https://marcus.io/blog/a11y-app-menu-button)时，你会注意到我添加了一个关于我的“vue-menu-button”所基于的 [WAI ARIA 创作实践的更新。](https://www.w3.org/TR/wai-aria-practices-1.1/#menu)[情况不是很清楚，关于这个话题有很多经验和看法](https://github.com/w3c/aria-practices/issues/353)。在有更多关于这个主题的用户数据之前，我决定从帐户按钮中移除菜单/菜单项模式，并使用一个更简单的解决方案来代替:细节和摘要([我写了一篇小笔记，介绍 GitHub 如何将这些元素用于他们的菜单结构](https://marcus.io/blog/progressive-enhanced-menus-details-summary) - [，尽管依赖于菜单/菜单项模式](https://twitter.com/muanchiou/status/1093243419948912645))。尤其是我到目前为止只添加了特定菜单的链接。

所以，我的下一步是在 Vue 中实现`<details>`和`<summary>`。这是非常容易的，由于元素板上切换行为。这里是结构:

```
<details ref="details">
    <summary>I'm the trigger</summary>
    <div class="content">
        Here goes the content that is initially hidden
        but visible once details has the open attribute
    </div>
</details> 
```

HTML 方面发生的事情是:一旦 summary 元素被激活(通过点击、触摸、输入、空格键),`open`属性作为一个整体被添加到`details`元素中。为了实现一个“菜单外观”，我只需要设计我打算用作“菜单内容”的内容

```
[open] .content {
  position: absolute;
  background-color: #ffffff;
  min-width: 320px;
  padding: 10px 0;
  border: 1px solid #2368a2;
  animation: slide-down 0.2s ease;
  box-shadow: 4px 4px 6px 0 #6665654d;
} 
```

`summary`元素在未设置样式时包含一个插入符号。您可以通过
删除它

```
summary {
  list-style: none;
} 
```

我添加的最后一件事是我为菜单按钮构建的一个特性:在我的 details/summary 构造之外单击会删除 open 属性，从而关闭菜单。这是我必须处理 Vue 和 JavaScript 的第一个“真正的”部分，它就像:
一样简单

```
<script>
export default {
  name: "NavigationMenu",
  created() {
    // On components creation, add click event listener to document
    document.addEventListener("click", this.documentClick);
  },
  methods: {
    documentClick(e) {
      // Get the details element
      let el = this.$refs.details;

      // Check if click happend inside component
      let target = e.target;

      // If not, close
      if (el !== target && !el.contains(target)) {
         this.$refs.details.removeAttribute("open");
      }
    }
  }
};
</script> 
```

我将对帐户(将包含链接)和购物车按钮都使用这种方法。这就是为什么我用本文中描述的结构、样式和“行为”制作了一个组件。

你可以在[https://vuejs.accessible-app.com](https://vuejs.accessible-app.com)上看到前面提到的按钮。实际上，还有很多要看的(和要听的)。但是正如我上面所写的——我将在单独的文章中解释购物车的其他特性。

现在，让我们总结一下。一旦我找到时间，我很可能会将我的 light Vue 细节/摘要包装器作为一个独立的组件发布，它也通过添加 polyfill 来处理[浏览器支持](https://caniuse.com/#search=summary)。但是在示例应用程序生命的这一点上，使用细节/摘要作为这些菜单目的总体上感觉是正确的。