# SSR 与 Sapper 和 Svelte

> 原文：<https://dev.to/itminds/ssr-with-sapper-and-svelte-1gp6>

[![](img/3812df7e65d54563a2872813ab04b46b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SOs1hfwA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qk28pd2r8vn7ppf3hnsx.png) 
Sapper 是实现苗条身材的完整开发者体验。

你可能不知道，Svelte 是一个“新的”前端框架/编译器，旨在编译自己离开。

这只是对 Sapper 和 Svelte 的一个介绍，也是为什么我认为它是一个很酷的新技术。

Svelte 的创始人 Rich Harris 提出，框架不应该让开发人员通过键入样板代码之类的东西来完成工作。因此，他的目标是只声明什么是必要的，然后让编译器解决剩下的问题。

因此，绑定到 HTML 元素变得非常简单。

```
<script>
let value = "";
</script>

<input bind:value={value}/> 
```

如代码片段所示，不需要绑定到上下文或任何其他内容。甚至可以把这个`<input bind:value />`写成一种简写，因为脚本属性叫做 value。

当这段代码编译时，Svelte 将看到 value 属性将在 dom 中被引用，并订阅来自 dom 的更改。Svelte 将生成所需的 JavaScript 来实现这一点。

如果我只引用一次 input 元素中的值，那么 Svelte 编译器只会生成单向绑定的代码。相当整洁。

苗条为餐桌提供了许多不同的新事物。其中一个主要原因是，由于这是一个“编译器”, Svelte 可以为他们想要的一切提供实用程序。

现代框架，如 Vue 和 React.js，都试图展示它们有一个小的包大小。因此，他们也只提供他们框架所必需的东西。这意味着如果你想要一个动画库，你必须得到它。而 Svelte 就不是这样了，因为 Svelte 只把你用的东西编译到 app 里。

> Svelte 基本上是一个非常激进的
> 
> 树摇动编译器/捆绑器

然而，Svelte 没有提供创建静态路由的方法。这就是 Sapper 的用武之地。

Sapper 就是你如何用苗条的身材写一个 SSR 应用程序。然后路由遵循一个文件夹结构，其中 routes/将是应用程序的根，而 routes/users/[slug]。js 应该是 users/kjf 1231 kdk，其中 kjf 1231 kdk 是一个用户的 id。

Sapper 甚至提供了一种将数据预加载到路由中的方法，方法是在路由到该路由的链接中添加一个
`<a rel=prefetch href='blog/what-is-sapper'>What is Sapper?</a>`。[工兵证件](https://sapper.svelte.dev/docs#Preloading)

Sapper 最酷的特性之一是它提供了一种将 SSR 应用程序“导出”为静态代码的方法。它通过启动服务器，然后爬行它，然后，对于它找到的每条路线，生成静态 HTML 页面和 JavaScript 来合并应用程序的其余部分。这意味着我们现在有了一种从静态文件服务器托管 SSR SPA 的方法，这非常简单。

我强烈建议你去看看 Sapper 和 Svelte 以及他们的文档。

我在 Sapper.js 中做了一个非常简单的激励应用程序。我花了大约 15 分钟来创建代码，然后基本上只需要 1 分钟就可以部署到 Netlify 上。[链接](https://elimone.party)

PS 域名是 elimone.party，因为我为我的妻子和她的朋友 Eli (eli+simone = elimone)创建了这个网站。没有提到一些人所知的柠檬党。对于那些感兴趣的人，代码在 GitHub()上