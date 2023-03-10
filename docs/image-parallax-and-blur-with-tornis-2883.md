# 使用 Tornis 的图像视差和模糊

> 原文：<https://dev.to/teej/image-parallax-and-blur-with-tornis-2883>

这最初是在我的网站上发布的。

注意——对于较大的东西最好不要模糊，因为它会破坏 Firefox 的性能。转换仍然运行良好。

...

我最近更新了我的网站，想把更多的注意力放在图片上。为此，我在文章中为封面图片留出了空间。它们占据了页面顶部的一大块，我可能会在某个阶段重新考虑这种方法。也许我会在图片上覆盖标题和描述...

我想加入一点活力，这张图片是首选。这时我想起了 [Tornis](https://tornis.robbowen.digital/) ，一个可以让你对视窗中的变化做出反应的库。我想用它来监视滚动位置，虽然我可以给`window`添加一个事件监听器并做同样的事情，但我还在考虑这个库的一些其他用例，所以我想为什么不试一试。

这是我在我的网站上使用的代码的精简版本。实际上，在开始任何事情之前，我会检查封面图片是否存在。我按照 Tornis 站点上的示例来到这里。

```
const { watchViewport } = tornis
const coverImage = document.querySelector('.js-cover-image')

const updateValues = ({ scroll }) => {
  if (scroll.changed) {
      let scrollOffset = scroll.top / coverImage.clientHeight

      scrollOffset = scrollOffset < 0 ? 0 : scrollOffset
      scrollOffset = scrollOffset > 1 ? 1 : scrollOffset
      document.body.style.setProperty('--scrollY', scrollOffset)
    }
}

watchViewport(updateValues) 
```

Enter fullscreen mode Exit fullscreen mode

我正在检查我们是否已经滚动过封面图像，并在`scrollY`自定义属性中设置一个介于 0 和 1 之间的值。使用这个自定义属性，我将设置自定义图像的`filter`和`transform`属性。

我还使用了`position: sticky`,这样图片就可以和我们一起在页面上移动了。

```
.cover-image {
  position: sticky;
  top: 0;
  height: 240px;
  overflow: hidden;
  filter: blur(calc(var(--scrollY) * 10px));
  transform: translate(0, calc(var(--scrollY) * -30px));
} 
```

Enter fullscreen mode Exit fullscreen mode

对于模糊，我将`scrollY`乘以 10 个像素。所以当`scrollY`是`0.3`时，将会评估为`3px`模糊。变换以同样的方式进行，因此它会沿着与滚动相反的方向缓慢移动。

你大概能想到更有创意的用法，比如 Tornis 主页上的神奇森林滚动。

[https://codepen.io/tjFogarty/embed/qGyqwa/?height=600&default-tab=result&embed-version=2](https://codepen.io/tjFogarty/embed/qGyqwa/?height=600&default-tab=result&embed-version=2)