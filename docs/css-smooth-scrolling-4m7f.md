# CSS 平滑滚动

> 原文：<https://dev.to/pqina/css-smooth-scrolling-4m7f>

去年，在构建 [FilePond 产品页面](https://pqina.nl/filepond/)时，我偶然发现了 scrollIntoView API。这是指示浏览器将元素滚动到视口中的一种简便方法。

> 本文最初发表于[我的个人博客](https://pqina.nl/blog/css-smooth-scrolling/)

通过在`scrollIntoViewOption`对象上添加`behavior`属性，可以指示 [`scrollIntoView`](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) API 为滚动部分制作动画。

```
element.scrollIntoView({ behavior: 'smooth' }); 
```

Enter fullscreen mode Exit fullscreen mode

我很快跳上我的 JavaScript 马，写了一个小脚本来自动检测锚的点击，这样浏览器就可以动画显示向锚目标的跳转。这种跳跃真的会让人迷失方向，所以动画化这个过程将会大大改善用户体验。

[scrollIntoViewOption 目前只在 Firefox 和 Chrome 上运行](https://caniuse.com/#feat=css-scroll-behavior)。

[https://codepen.io/rikschennink/embed/ZjBGda?height=600&default-tab=js,result&embed-version=2](https://codepen.io/rikschennink/embed/ZjBGda?height=600&default-tab=js,result&embed-version=2)

[![Noice!](img/5c26e3909083fa84f4324f275fbdc84f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oB_Jm0V3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h9rjdbd131sseypv7n1y.gif)

我[在 Twitter](https://twitter.com/rikschennink/status/1019960938194259976) 上发布了它，然后就收工了。

然后， [Hans Spie 指出这也可以用 CSS](https://twitter.com/hans_/status/1020081742277902336) 做到，什么！？

原来有一个 [`scroll-behavior`](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-behavior) CSS 属性，我们可以设置为`smooth`，就是字面上的意思。这几乎就像`awesome: yes-please`。我们可以将`scroll-behavior`属性设置为我们想要展示平滑滚动行为的容器，这样就完成了。

我只用 CSS 创建了一个新的演示。

[![Smooth scroll comparison](img/fbdd1ecd747c5d848451a7b4d72007fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pa3SK6e4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qci4smo6xj0fazxwuz9t.gif)

在我们疯狂地将它应用到我们所有的网站之前，有几件事我们需要记住。

### 滚动距离关系重大

如果有很长的距离，Firefox 会跳过内容以保持滚动时间有限，而 Chrome 有最大速度，只需要花时间到达目标。

[![Long distance scroll comparison](img/cb3da6e7793bf19d19f2e43e54263d5c.png)](https://codepen.io/rikschennink/details/QBZByx)

我们可以使用[智能 CSS](https://dev.to/rikschennink/applying-styles-based-on-the-user-scroll-position-with-smart-css-5c) 来检测长页面，并有条件地应用平滑滚动样式。

### 可达性

人们看动画时可能会晕车。为了避免这种情况，您可以将 CSS 属性包装在一个 [`prefers-reduced-motion`](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) 媒体查询中。可惜 Chrome 不支持这个。Safari 支持，但 Safari 不支持平滑滚动。

```
.my-smooth-container {
  scroll-behavior: smooth;
}

@media (prefers-reduced-motion: reduce) {
  .my-smooth-container {
    scroll-behavior: auto;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

当决定一个新的功能时，我们不应该马上使用 JavaScript。我们应该先做一个快速搜索，看看 CSS 是否也能做到这一点。属性可以是一个很好的 UX 改进，一定要确保在很长的页面上禁用它，并提供一个禁用它的选项来保持页面的可访问性。