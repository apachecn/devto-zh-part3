# 从小项目中学习

> 原文：<https://dev.to/iam_timsmith/learning-from-small-projects-59l2>

我最近在为一个客户做一个项目，我在 WordPress 后端使用 Gatsby.js。我用 Netlify 来托管 Gatsby 网站，运行良好，但每次 WordPress 中的内容更新时，它都需要重建网站。

我的解决方案是创建一个定制的 WordPress 插件，这样可以很容易地与 Netlify 进行交互，并在用户更新内容时使用 webhook 来重建网站。此外，我在仪表板上创建了一个小部件来查看 Netlify 站点的当前状态。

构建这个插件教会了我更多关于构建 WordPress 插件的知识，并给了我一个工具，在我需要使用 WordPress 和 Netlify 的未来项目中使用(我希望会变得更频繁)。

作为一名开发人员，您构建了哪些小项目来帮助学习新技术或解决小问题，并帮助您前进？

[![WP Trigger Netlify Build](img/e50a886ae0133916194464eed5153cef.png)](https://github.com/iamtimsmith/wp-trigger-netlify-build) 
附:如果你有兴趣看或者用这个插件，[可以在这里找到](https://github.com/iamtimsmith/wp-trigger-netlify-build)。