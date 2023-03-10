# 没人需要的建筑

> 原文：<https://dev.to/gregnavis/the-architecture-no-one-needs-2jf0>

最初发布于[我的网站](https://www.gregnavis.com/articles/how-to-reduce-test-interference-in-minitest.html)

如今单页应用程序风靡一时。许多人称赞它们模糊的技术优势，却忽略了巨大的开发成本。

在本文中，我们将讨论为什么单页应用程序几乎总是比多页应用程序差，并简要介绍可以在没有成本的情况下产生类似收益的替代方案。

## 软件的业务

每项业务都有两个方面:收入和成本。与其他选择相比，水疗是否是一项好的投资取决于它如何影响底线。

收入取决于交付给用户的价值，而价值又主要取决于功能集。架构选择本身并没有为用户提供价值。spa 的承诺是更好的用户体验，这可以转化为更高的收入。这种增加必须与相应的成本增加进行比较，以评估投资是否值得。

本文试图证明，与精神创伤和痛苦相比，水疗的成本是巨大的，主要是因为附带的复杂性更大。然而，许多公司盲目地认为用户体验已经改善到足以证明额外费用的合理性。其他人将他们的选择建立在一些模糊的工程纯度的基础上，而没有考虑商业因素。

这篇文章有两个要点:

1.  不要考虑 SPA 架构，除非有证据表明用户体验是你的应用程序的头号问题，即使在这种情况下，也要考虑替代方案。例如，如果你需要让应用程序更快，那么你可以通过调整数据库、缓存、使用 CDN 等来获得大部分好处。
2.  MPA 是一种竞争优势。

我们来看看成本方面。

## 单页应用的价格

架构选择以不同的方式影响开发的不同方面。这就是为什么我编制了一个受 SPA 架构负面影响的区域列表。你可以用它来评估一个 SPA 已经或将要对你的项目产生的影响。

让我们强调一个清晰的模式:**SPA 会对列表中的大多数项目产生负面影响，并且需要额外的工作来恢复默认情况下 MPAs 中存在的功能**。

以下是从最昂贵的物品开始的清单:

Statefulness

I think this is a very underappreciated aspect of SPAs. Stateful software is always more difficult to work with than stateless. The frontend state is added on top of the already-existing backed state. This requires more development time, increases the risk of bugs, and makes troubleshooting more difficult.

Testing

The stateful nature of the frontend greatly increases the number of test cases we need to write and maintain. Additionally, the test setup is more complicated because we need to make the backend and frontend talk to each other.

Performance

It's frequently claimed SPAs offer better performance but it's more complicated than commonly thought. An API-only backend renders and sends less data than an MPA but the network latency is still there and the app won't be faster than that. We could work around the issue by implementing optimistic updates but this greatly increases the number of failure modes and makes the app more complex.

Slow First-Time Load

This is a well-known problem that isn't fully understood. The usual claim is that after the browser caches the asset bundle everything will be snappy. The implicit assumption is we've stopped development and don't update the bundle. If we do then users may experience quite a lot of first-time loads in a single day.

Authentication

This is optional for an SPA but it seems <abbr title="JSON Web Tokens">JWTs</abbr> are a frequent choice for authentication. The claimed benefit is statelessness. It's all true but has a serious downside: we can't invalidate sessions unless we identify them on the backend which makes the system stateful. I think we always should be able to invalidate sessions. Therefore, because we need server-side state, we can simply use bearer tokens. They're simpler to understand, implement and troubleshoot.

Session Information

Again, this is optional but SPAs often use local storage instead of cookies. Its tremendous downside is lack of a mechanism similar to HTTP-only cookies. Given web apps often include scripts from third-party domains and CDNs a successful attack against them can leak session IDs and other secrets.

State Updates

Let's illustrate this with an example: we're building an e-commerce site that has a list of categories. We need to update the list from time to time. In an MPA, the list is updated on every page load. That's not the case in an SPA though. We need to think about an algorithm and implement it. It's not rocket science but it's busywork that users don't care about.

Error Handling

An MPA renders a 500 page upon error and that's it. However, an SPA needs to detect errors in the client code and then update the user interface accordingly. Again, busywork required to regain what MPAs offer out of the box.

Server-side Rendering

We may need server-side rendering so that users are able to discover the app. This is yet another area where you need to do work to match the capabilities of an MPA.

Protocols and Serialization

In an MPA, we can simply pass models to views and render attributes we need. This isn't the case in an SPA. We need to define data formats and implement serialization. Naturally, there are tools that can help but it's extra work and dependencies whose only effect is regaining the convenience of an MPA.

Tooling

Our build system may become more complicated because of the additional tooling and dependencies required to build an SPA.

Shared Metadata

We may need to share data between the frontend and the backend. For example, if the SPA consumes a REST API then we would like routing information to be derived from the same source. Again, this is unnecessary in an MPA.

如果你从商业角度来看 SPA 架构，那么你的成本会上升，但你不会看到更多的收入，因为**用户不关心技术选择**。结果是投资回报为负。

## 做什么相反

我的建议很简单:如果疼，就停止做。更好的是:一开始就不要做。

一个多页面的应用程序实现起来要简单得多，并且有许多优势，这些优势只有在花费巨大的 SPA 中才能复制。自然，更复杂的组件有时是不可避免的，但是有更明智的方法。

首先，开始使用[涡轮链](https://github.com/turbolinks/turbolinks)。这将使应用程序感觉更快，而不会增加额外的复杂性。它经常与 Ruby on Rails 联系在一起，但也可以很容易地独立于其他技术使用。

第二，对于更简单的组件，使用 [Stimulus.js](https://stimulusjs.org/) 。这是一个相对较新的发展，但我有机会实现了十几个刺激控制器，体验非常好。

第三，如果你正在实现一个非常复杂的组件，那么你可以只为那个组件使用 React **。例如，如果您正在构建一个聊天框，那么就没有必要在 React 中实现您的登录页面。这同样适用于 Vue.js 和包的其余部分。**

## 总结

单页应用比多页应用贵得多。在大多数情况下，没有商业理由选择这种架构。它试图解决的问题可以用更简单的方法解决，而不需要过多的成本和复杂性。在*有些*案例中，SPA 是有意义的，但这是另一篇文章的主题。