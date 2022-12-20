# 库 vs 框架:我自己的两分钱

> 原文：<https://dev.to/amexboy/library-vs-frameworks-my-own-two-cents-569o>

大约一周前，我读了一篇名为“[无论如何，这就是为什么我更喜欢 Vue 而不是 React](https://itnext.io/anyway-this-is-why-i-prefer-vue-over-react-ad2653595fc5) ”的精彩博客。

他称 Vue 为图书馆，对此我另有评论。这引发了一场争论，这场争论一直持续到“让我们保留各自的观点”。

这是我的想法

Vue 是一个框架。

在我看来，框架和库的主要区别在于谁调用谁。试镜不算。

框架强加了一种结构，客户必须遵循这种结构才能工作。它们取决于您为管理活动的生命周期而实现的结构。Vue 就是这么做的。[让我解释一下为什么](https://www.youtube.com/watch?v=KdlYHsMe2rk)

当您定义一个 Vue 组件时，您定义了一些方法和对象。

```
new Vue({
  data: () => {
    return: {}
  },
  computed: {},
  mounted: () => {
  }, 
  watch: {
  }
}) 
```

如果这是 java 或任何其他真正的面向对象系统，您可能已经扩展了一些基类或实现了一个接口。注释你的方法或者有时只是遵循一些命名规则(比如 Vue)。然后框架决定何时调用什么。

当你使用一个库的时候，你将不会实现任何结构或者 API，你将会调用库的 API。