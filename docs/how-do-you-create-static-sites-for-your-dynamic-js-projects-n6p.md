# 你如何为你的动态 JS 项目创建静态站点？

> 原文：<https://dev.to/dannyaziz97/how-do-you-create-static-sites-for-your-dynamic-js-projects-n6p>

所以我有一个 preact SPA 应用程序，我想为一些内容创建静态网站(内容每年只更新几次，新内容每周只添加一次)

目前，我正在使用`prerenderer-webpack-plugin`来预渲染构建中需要的所有路径，然后使用 webhooks 和 Netlify 来构建变更。

我很想知道其他人是如何看待这件事的？