# 穷人的黑暗模式使用 CSS 滤镜

> 原文：<https://dev.to/pqina/poor-man-s-dark-mode-using-css-filter-211n>

轻按右上角的“黑暗模式”复选框。

[https://codepen.io/rikschennink/embed/GLMLjr?height=600&default-tab=result&embed-version=2](https://codepen.io/rikschennink/embed/GLMLjr?height=600&default-tab=result&embed-version=2)

复选框只切换`<html>`元素上的`dark-mode`类。

`dark-mode`类是做什么的？

```
html.dark-mode {
    filter: invert(100%);
}

html.dark-mode img {
    filter: invert(100%);
} 
```

Enter fullscreen mode Exit fullscreen mode

设置为`invert(100%)`的`filter`属性将反转页面上的所有颜色。因为它也被分配给了`img`(所有的图像都在`<html>`元素的子树中)，它将再次反转每个`<img>`元素的颜色，防止图像看起来像照片底片。

瞧啊。🚀