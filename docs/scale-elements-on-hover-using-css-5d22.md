# 使用 CSS 在悬停时缩放元素

> 原文：<https://dev.to/paramharrison/scale-elements-on-hover-using-css-5d22>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

我们经常需要缩放悬停时的图像或内容块，这可以通过使用 CSS 的`transform`属性
来实现

```
// Growing in size
.grow:hover {
  transform: scale(1.2);
}
// shrinking in size
.shrink:hover {
  transform: scale(0.8);
} 
```

这里单靠`transform: scale`属性并不能帮助效果平滑，为了得到平滑的效果，我们需要在元素中加入一些`transition`。

```
.grow,
.shrink {
  transition: all 0.3s ease-in-out;
} 
```

[https://codepen.io/Param-Harrison/embed/MLypze?height=600&default-tab=result&embed-version=2](https://codepen.io/Param-Harrison/embed/MLypze?height=600&default-tab=result&embed-version=2)

这种技术在没有厂商前缀的情况下有广泛的浏览器支持。下次在你的 UI 中需要的时候，请随意使用它👍

> 注:本文最初是为[我的博客](https://learnwithparam.com/blog/scale-elements-on-hover-using-css/)写的。我在这里为神奇的开发者社区重新发布它。