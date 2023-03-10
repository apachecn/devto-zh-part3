# 使用智能 CSS 为你精彩的时事通讯弹出窗口计时

> 原文：<https://dev.to/rikschennink/using-smart-css-to-time-your-wonderful-newsletter-popup-2hdo>

在之前关于智能 CSS 的文章中，我们使用用户的滚动位置来触发一个浮动的导航标题。

> 本文最初发表于[我的个人博客](https://pqina.nl/blog/using-smart-css-to-time-your-wonderful-newsletter-popup/)

让我们更进一步，看看我们是否可以使用这种技术来确定用户是否有兴趣订阅我们的时事通讯。想象一下，这是一个网站上的时事通讯，向用户呈现一篇文章，就像你现在正在阅读的这篇文章。目标是触发用户订阅，这样如果将来有新文章发表，我们就可以发送通知。

我们会假设市场部拿枪指着我们，命令我们显示一个带有订阅表单的弹出窗口。就个人而言，我更愿意在文章下面显示表单，内嵌在页面中，但是您可能知道这些事情是如何进行的。

## 如何确定显示此弹出窗口的最佳时刻？

想象一下，你在现实生活中购物，你随意走进一家商店，你甚至还没有跨过门阶，店主就跳到你面前，问你是否想收到他们的每月简讯。这会很奇怪，对吧？

**在网络上，上述行为不知何故成了常态。**

也许在我们提问之前，先评估一下用户对我们时事通讯的兴趣是一个好的开始。我们如何确定这种潜在利益？让我们回到我们的随机商店。

假设以下情况:

*   我以前去过这家商店
*   我花了一些时间逛商店
*   我实际上已经购买了一些东西
*   我和店主进行了积极的互动

如果其中几个有绿灯，我仍然不会欢迎店主跳到我面前，但我肯定不会介意他们在柜台前问我，如果发生这种情况，我甚至可能会订阅。

在我们的网络环境中有类似的东西吗？我能想到几个数据点，但如果你有任何其他建议，请在评论中告诉我。

*   用户在网站上花了一些时间
*   用户一直在阅读该页面
*   用户在当前页面上花了一些时间
*   用户已经进行了购买

## 将这些数据点转化为变量

在我们使用`localStorage`将用户在我们网站上花费的时间存储在一个会话变量中之前，确定用户是否访问过网站。通过计算在每个页面上花费的时间，并以一定的时间间隔保存到`localStorage`中，我们可以知道用户浏览网站有多长时间了。因此，我们可以确定用户对我们的平台有多熟悉，我们是一个随机的店主吗？还是我们以前见过？

通过测量用户在页面上向下滚动了多远，我们可以确定他是否正在与网站进行交互。如果页面显示一篇文章，这可能会给我们一些关于用户已经阅读了这篇文章的信息。结合用户在页面上花费的时间，我们可以猜测用户已经阅读了这篇文章或者即将阅读完。如果用户花时间阅读了这篇文章，那么这可能意味着他对我们的内容感兴趣，可以说他已经“购买”了我们的内容。当然，如果你经营一家网上商店，你也可以跟踪实际购买情况。

下面演示中使用的技术与前一篇文章中的大部分相同。我引入了一个函数作为数据属性的更新器。还有一个新增功能是`stack`功能。

```
const stack = (value, step) => {
  // round the value to the nearest value divisible by step
  // if value is 37 and step is 10 the result is 30
  value = Math.floor(value / step) * step;

  // 30 will be our first value
  const parts = [value];

  // now we subtract 10 each iteration till we reach 0
  while (value > 0) {
    value -= step;
    parts.push(value);
  }

  // reverse the array so it goes from 0 to 30 insted of 30 to 0
  return parts.reverse();
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数接收一个`value`和一个`step`参数。假设`value`被设置为`50`，`step`被设置为`10`，函数将返回这个数组:`[0, 10, 20, 30, 40, 50]`。我们使用这个数组在数据属性中存储多个值。

例如，假设用户已经滚动了页面的 50%。然后将`data-vertical-scroll-progress`属性设置为`"0 10 20 30 40 50"`。这允许我们使用 [CSS 属性选择器](https://developer.mozilla.org/en-US/docs/Web/CSS/Attribute_selectors)来确定用户是否至少滚动了页面的 30%。`~=`比较检查设定值是否包含在空格分隔的项目列表中。

```
<html data-vertical-scroll-progress="0 10 20 30 40 50">
    <!-- Your website here -->
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
html[data-vertical-scroll-progress~='30'] {
   /* user has scrolled down 30% */
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们结合我们所有的测试，使这成为现实。下面的演示将显示当你向下滚动 50%并在页面上停留 30 秒时的弹出窗口。

[https://codepen.io/rikschennink/embed/vbKjmJ?height=600&default-tab=js,result&embed-version=2](https://codepen.io/rikschennink/embed/vbKjmJ?height=600&default-tab=js,result&embed-version=2)

我对我们使用这些技术的其他方式非常感兴趣，所以如果你有任何想法，请在下面分享！