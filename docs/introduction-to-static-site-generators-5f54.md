# 静态网站发布者简介

> 原文：<https://dev.to/spekulatius1984/introduction-to-static-site-generators-5f54>

这篇文章最初发布在[我的个人博客上，介绍静态站点生成器](https://peterthaleikis.com/posts/introduction-to-static-site-generators/)。这是 DEV 的调整版本:

即使在 2019 年，许多开发者也习惯于为内容网站开发 WordPress(或类似的软件)。直到最近，我一直是这些开发者中的一员。我愿意开发一个网站，利用技术来完成我不需要的工作。我不会选择 WordPress 作为框架，但最终结果是一样的:构建一个系统来交付静态发布软件可以轻松处理的东西。在许多情况下，设置、配置和维护一个成熟的内容管理系统并将内容存储在数据库中是不值得的。

> 2019 年是静态发电机年。今天就开始写博客吧！

## 什么是静态站点生成器？

静态站点生成器(也称为静态发布器)是一个程序，它生成一个完整的网站作为输出。生成可以在本地计算机上完成，也可以在部署期间在 web 服务器上完成。然后网站通过你的网络服务器提供服务——就像过去一样。

内容通常以模板语言编写，如 Markdown 或 Liquid。这可以让你专注于写作，而忽略内容的布局和风格。一些静态生成器允许你同时使用 [PugJS](https://pugjs.org) 、Markdown 和其他工具来混合和匹配你的模板。作为一个静态生成的网站，你可以在你的电脑上随时阅读你的内容。如果你想重新使用它或重新发布，这意味着非常努力地寻找和修改你自己的内容。

由于没有使用数据库(除了文件系统之外),所以没有使用 SQL 或提取插件。这使得您的网站部署更加稳定和健壮。有了 GitHub 和 co .你还可以随时编辑你的网站，只需提交新的内容就可以部署。

## 为什么要使用静态站点生成器？

*   安全性:没有数据库或后端应用程序。这减少了攻击的表面，因此使您的安全性非常好🔐️

*   性能:由于没有后端应用程序为您的网站提供服务，因此使用的内存和 CPU 更少——您的网站在高负载下停留的时间更长💪️

*   默认情况下进行备份:您将始终拥有一份与 web 服务器分离的内容副本(通常在 git 中)。这确保了你的站点在服务器硬盘故障和你的实时站点离线的情况下很容易被复制。

*   轻松迁移:将你的网站迁移到另一家托管公司，没有太多痛苦:将输出(HTML，CSS，JavaScript)推送到新的服务器上，重新配置 DNS，你就一切就绪了。

*   轻松选择网站托管:你不必担心支持的技术或 PHP/Python/Ruby 版本，因为你只需要提供静态内容。

*   即使不再是一个主要问题，静态网站的托管通常更便宜。大部分是免费的:通过 [Netlify](https://netlify.com) 和 [GitLab](https://about.gitlab.com/product/pages/) ， [GitHub](https://pages.github.com/) 你可以免费获得静态托管。

## 应该选择哪个静态站点生成器？

有许多静态站点生成器可供选择。您的决定更多地取决于对框架的偏好，而不是技术差异。我个人最喜欢的是:

*   非常简单明了。你可以在几分钟内[开始使用 11 号](https://www.11ty.io/docs/getting-started/)。默认支持 pug，markdown，当然还有普通 HTML。我的[个人网站](https://peterthaleikis.com)是基于 Dan Urbanowicz 的[11 点出发。](https://github.com/danurbanowicz/eleventy-netlify-boilerplate)

*   [Nuxt。Js](https://nuxtjs.org/) : Nuxt。JS 是一个基于 NextJS 和 VueJS 思想的无配置框架。你可以用 VueJS 组件来编写完整的网站。只需一分钟多一点的时间，就可以观看 Nuxt 的[入门视频。](https://nuxtjs.org/examples)

*   VuePress : VuePress 是一个 Vue.js 驱动的静态站点生成器，它将 Vue 的强大功能与服务器端渲染(SSR)相结合。减价是这里的首选语言。编译后，你会得到一个单页的应用程序。附带[广泛而详细的文档](https://vuepress.vuejs.org/default-theme-config/#homepage)。

## 总结

我发现使用静态站点生成器的主要优势是易于启动和运行。设置和配置的工作量非常低。当天就可以开始写了。它很稳定，扩展性很好，非常适合制作网站。强烈推荐使用静电发生器，它完全符合我的需求和技能。话虽如此，我确信非开发者也会找到他们的路。对于你最后要做的任何在线项目，建议对 HTML 和 CSS 有所了解。我鼓励你四处看看，尝试一些静态站点生成器，找到最适合你需求的。如果你需要支持，考虑联系我的开发公司。我提供一系列服务，重点是 CMS 和应用程序开发。

如果你喜欢这篇文章，请考虑分享到 [Twitter](https://twitter.com/intent/tweet?text=Introduction%20into%20Static%20Site%20Generators%0A%0Ahttps%3A%2F%2Fpeterthaleikis.com%2Fposts%2Fintroduction-to-static-site-generators%2F&via=spekulatius1984) 或[脸书](https://www.facebook.com/sharer/sharer.php?u=https://peterthaleikis.com/posts/introduction-to-static-site-generators/)上。🙏️:如果你有任何问题，请[联系我](https://peterthaleikis.com/contact)。