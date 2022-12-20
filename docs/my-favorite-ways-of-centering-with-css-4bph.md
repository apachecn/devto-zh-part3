# 我最喜欢的 CSS 居中方式

> 原文：<https://dev.to/steffenpedersen/my-favorite-ways-of-centering-with-css-4bph>

# 我最喜欢的 CSS 居中方式

过去，我认为用 CSS 居中是非常棘手的事情。通常，我把事情弄得比现在复杂得多。根据具体情况有很多种做法。这些是我最喜欢的水平和垂直居中的方式。

## 位置绝对

第一种方法或途径是绝对定位。我们知道子元素的高度和宽度，然后我们可以使用负值的边距，即子元素高度或宽度的一半。

```
.absolute.parent {
  position: relative;
}

.absolute .child {
  position: absolute;
  top: 50%;
  left: 50%;
  margin-top: -110px;
  margin-left: -110px;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 变换翻译

但是如果不知道高度或者宽度呢？然后，您可以使用 transform 属性，在两个方向上进行 50%的负平移。你知道了吧！

```
.translate.parent {
  position: relative;
}

.translate .child {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
} 
```

Enter fullscreen mode Exit fullscreen mode

## Flexbox

我后来遇到的是 flexbox 方法。*真是天才！*你只需要用中心定义两个属性——也就是`justify-content`和`align-items`。*就是这样！如果你想要右下角的元素，你也可以写`flex-end`。Flexbox 实际上非常非常有趣。* 

```
.flexbox.parent {
  display: flex;
  justify-content: center;
  align-items: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你知道其他元素居中的方法，请在下面写下评论。

> 你使用另一种方法吗？🤔你用 Sass mixins 或者其他很酷的东西吗？🤔

感谢您抽出时间！

如果你喜欢这个，那么请❤️和 [*在 Twitter 上关注我*](https://twitter.com/mrsteffenp) 。