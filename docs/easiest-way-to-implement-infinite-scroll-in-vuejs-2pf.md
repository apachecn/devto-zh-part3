# 在 Vue.js 中实现无限滚动的最简单方法

> 原文：<https://dev.to/fayazara/easiest-way-to-implement-infinite-scroll-in-vuejs-2pf>

[![](img/5ce0824901953b6cfb6b6af8f898ae90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oXWZJfWm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhETlL_GOAicLSX6ojL7I6w.png)

作为一名 jQuery 开发人员，通过阅读所有关于 React、Vue 和 Angular 的内容，你会得到严重的 FOMO，我也开始学习 React，但它对我来说有一个陡峭的学习曲线，因为我在工作日全职工作时没有足够的时间学习这个&周末是一名自由职业者。

我在 Udemy 上购买了一门课程，希望有一天能学会它，但它已经被遗忘了。是的，我知道 React 有多受欢迎，但不知何故我选择了学习 Vue，因为它比 React 简单得多，你可以通过查看别人的代码来理解，并对它的工作有一个简单的想法&就像在你的 HTML head 标签中添加一个脚本一样简单，你就可以开始了，没有 Webpack，没有构建麻烦。

来自 jQuery，这个新的 Javascript 世界非常令人生畏，因为我曾经将我的 jQuery(Javascript)代码与我的 HTML 代码隔离开来，并且我从我的脚本标记中操纵我的 DOM，但是这里完全不同。这里的 Javascript 混合了我的 HTML 标记(我不是在说内联 JS)。你可以循环 HTML 标签，在条件上显示

,想想 PHP，但是所有这些都在你的浏览器中完成。

关于 Vue 说得够多了，让我们来构建一个小项目。我们将建立一个应用程序，我们将从一个 REST api 获取数据，并显示在我们的 DOM 上。它应该启用无限滚动。

> PS 我还在学习 vue.js，可能用不寻常的方式写了些东西。

让我们从在我们的 HTML 文件中添加 Vue，Axios 和布尔玛 cdn 开始&我已经做了一个显示文章标题和正文的小卡片。

[https://codepen.io/fayazara/embed/drvxPY?height=600&default-tab=result&embed-version=2](https://codepen.io/fayazara/embed/drvxPY?height=600&default-tab=result&embed-version=2)

所以我们需要一些来自这个 [API](https://jsonplaceholder.typicode.com/posts) 的假 json，它会以 JSON 格式发送给你大约 100 个帖子，我们将使用 vue & axios 来使用这个 API 并显示这些卡片中的数据，就像上面一样。

我们将调用 api 并将响应存储在名为 posts 的数据变量中。我们需要在页面加载后立即获取数据，在 vue [生命周期](https://vuejs.org/v2/guide/instance.html)中有一个叫做 created()(类似于 jQuery 中的 document.ready)的东西，这个函数会在文档创建后立即为我们运行一个函数。一旦我们有了数据，我们将使用 v-for 指令循环遍历这些数据，并创建多个如上的卡片并显示它们。

[https://codepen.io/fayazara/embed/eXvqdX?height=600&default-tab=result&embed-version=2](https://codepen.io/fayazara/embed/eXvqdX?height=600&default-tab=result&embed-version=2)

在这里，您可以看到 axios 已经接收到了 json 的数据，vue 的 v-for 特性遍历了数组并显示了数据。

我们已经完成了第一个目标，从远程 api 获取数据并显示在我们的网页上。

但这有一个问题，这是一个巨大的列表，有时将大量 HTML 动态附加到 DOM 会导致很多问题，想想一台旧电脑或手机，你在网页上附加了 100 多行数据，这可能会使你的浏览器崩溃。

一个巧妙的解决方案是使用无限滚动，这将只向用户显示有限的行数，当你到达页面底部时会显示更多。

### 如何实现无限滚动？

我们将检查屏幕底部是否可见滚动监听器功能类似这样。

[![](img/6478767da2d2ab382d9b73a979d99d56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gvoZGqql--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYr0rt29T35lcdlEPtgR09A.png)

明白了吗？

这是一种方法，但是我发现了一个小的 Vue 插件可以完美地做到这一点

[eleme Fe/vue-infinite-scroll](https://github.com/ElemeFE/vue-infinite-scroll)

这甚至支持与 CDN 集成，并跳过所有构建部分。让我们从上面把这个加到我们的笔里。

[https://codepen.io/fayazara/embed/PLmRLz?height=600&default-tab=result&embed-version=2](https://codepen.io/fayazara/embed/PLmRLz?height=600&default-tab=result&embed-version=2)

为了制作列表视图的动画，我使用了一个名为 [AOS](https://michalsnik.github.io/aos/) 的 Javascript 库，在滚动时制作动画，这是最简单的制作动画的方法。

[AOS——卷轴库上的动画](https://michalsnik.github.io/aos/)

现在，我们通过 Axios 使用了 REST api，并使用 vue awesome v-for 指令很好地显示了它，并使用 vue 无限滚动插件使它变得更好。

如果你觉得这篇文章有用，请分享。