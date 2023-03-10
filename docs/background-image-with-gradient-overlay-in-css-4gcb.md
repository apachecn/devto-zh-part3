# CSS 中带有渐变覆盖的背景图像

> 原文：<https://dev.to/paramharrison/background-image-with-gradient-overlay-in-css-4gcb>

> 在 [Twitter](https://twitter.com/learnwithparam) 上关注我，很高兴接受您对主题或改进的建议

覆盖背景图片是非常常见的用户界面功能。在英雄，卡牌组件上会看到更多。

有几种方法可以实现它。最优选的方法是利用 CSS 伪元素来分配具有适当不透明度的背景颜色。这种方法不需要额外的 HTML 元素，它适用于所有现代浏览器。

## 背景图片带叠加

```
.bg-img-overlay {
  width: 600px;
  height: 600px;
  position: relative;
  // Image as background
  background: url('https://unsplash.it/600x600') center center no-repeat;
  background-size: cover;

  &:before {
    // Pseudo element will only render if it has a content property
    content: '';
    // Overlay should occupy the full width and height of parent image container
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    // Overlay background-color
    background-color: #000000;
    // Overlay opacity, else overlay won't be transparent and show the image
    opacity: 0.6;
  }
} 
```

[https://codepen.io/Param-Harrison/embed/yrgXvy?height=600&default-tab=result&embed-version=2](https://codepen.io/Param-Harrison/embed/yrgXvy?height=600&default-tab=result&embed-version=2)

> 感谢 [unsplash](https://unsplash.com) 及其贡献者慷慨提供图片

## 带有渐变叠加的背景图像

同上。唯一的变化是，覆盖图的`background-color`需要替换为具有线性梯度的`background-image`。

```
.bg-img-overlay {
  background: ...;
  &:before {
    ...
    // Overlay  background-color  background-color: linear-gradient(to bottom right, #002f4b, #dc4225);
    ...
  }
} 
```

[https://codepen.io/Param-Harrison/embed/EvEmmW?height=600&default-tab=result&embed-version=2](https://codepen.io/Param-Harrison/embed/EvEmmW?height=600&default-tab=result&embed-version=2)

> 注:我在 codepen 中使用了`vw`和`vh`作为宽度和高度，以使其看起来更漂亮。您可以更改图像的宽度和高度属性，并查看适用于不同大小的示例。

在创建许多 UI 组件时，它是非常简单和有用的技术😎

> 注:本文最初是为[我的博客](https://learnwithparam.com/blog/bacground-image-with-gradient-overlay-in-css/)写的。我在这里为神奇的开发者社区重新发布它。