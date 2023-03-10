# 设置默认全局链接的样式，而不影响组件链接

> 原文：<https://dev.to/hybrid_alex/style-your-default-global-links-without-affecting-your-component-links-39a9>

[https://www.youtube.com/embed/K5rmQXlIomk](https://www.youtube.com/embed/K5rmQXlIomk)

这是我最近在任何新项目中使用的一个小技巧。样式化你的默认全局链接*而不影响你的组件链接*。

```
a:not([class]) {
  color: #0055ff;
  text-decoration: none;

  &:hover,
  &:focus {
    text-decoration: underline;
  }
} 
```

这有助于避免覆盖您在组件链接中应用的全局默认链接样式。

查看下面的演示:

[https://codepen.io/alexcarpenter/embed/eXNEeo?height=600&default-tab=result&embed-version=2](https://codepen.io/alexcarpenter/embed/eXNEeo?height=600&default-tab=result&embed-version=2)