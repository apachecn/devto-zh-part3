# tflookup -开发者日记第 4 部分

> 原文：<https://dev.to/dploeger/tflookup-developer-diary-part-4-3pb9>

亲爱的日记！

所以我完成了我的 REST 服务，它将提供我的搜索结果。但是没有一个吸引人的前端有什么好处呢？

在去年的[艺术节](https://www.enterjs.de/)上，我几乎被铺天盖地的关于 [Vue.js](https://vuejs.org) 的好评击垮，之后我立即选择了 Vue-way。

我认为，与其他框架相比，Vue 非常简单，而且结构也非常好。

此外，它有适当的类型脚本支持和良好的文档。

所以我[用](https://github.com/dploeger/tflookup/tree/master/static) [Vue CLI](https://cli.vuejs.org/) 开始了一个 Vue 项目(它有一个惊人的新 UI btw)。

对 REST 调用的简单测试运行良好，但是我想要更吸引人的东西。

幸运的是， [Vuetify](https://vuetifyjs.com) 为 Vue 准备了一些不错的材质设计组件，还有一个不错的自动完成组件。

[这个方法](https://github.com/dploeger/tflookup/blob/master/static/src/components/Lookup.vue#L70)监视查询输入字段，并使用 REST 后端填充结果。我在那里使用了一个延迟承诺，允许用户在我使用文本搜索后端之前输入完整的搜索文本。

然后，autocomplete 用条目结果呈现了一个所谓的“slot”。

选择一个结果将触发实际 terraform 文档页面的位置更改。

这里最难的是让我理解 Vue 插件是如何工作的，它们如何在 *Typescript* 中工作，以及 Vue 化组件和它们的特性(比如插槽)是如何工作的。但是 Vuetify 和 Vue 的好例子和文档帮助很大。

你的丹尼斯

*这篇文章是 [tflookup 开发者日记系列](https://dev.to/t/tflookup)* 的五篇文章之一

*封面图片:弗雷德里克·鲁本森的《日记写作》*