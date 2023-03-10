# 装载微光！

> 原文：<https://dev.to/dhilipkmr/the-loading-shimmer-29dc>

当我听到 Loading Shimmer 这个词时，我认为这是 Web 开发中引入的一些新的很酷的东西。但事实证明，这是我们在日常网络浏览中已经注意到的事情。

当 DOM 内容由于较慢的网络而需要时间加载时，我们通常会显示一个加载图标/gif，表明内容正在被加载。展示装载机的传统方式:

[![Old Loading Icon](img/8ea3cb31ae5d95fa5b1a64aba336c2d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bIcIUu5D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t7u2rdii5u9n4zyqs2aa.jpg)

上面的例子很好地让用户知道内容正在被加载。但是，这并不是很好的用户体验。
那么，我们如何实现更好的用户体验呢？

### 骷髅屏幕前来救援。

[![](img/69b2002e269529dab2cd4ee639ccedcb.png)](https://i.giphy.com/media/uL5GcZnmxQ2kM/giphy.gif)

代替显示一个加载图标，我们可以有一个应用程序的样本布局，指示当内容被完全加载时，我们的内容是如何放置的。

就像下面的脸书

[![](img/2b58ffa70d8e1aa3129a98a95ed16ecc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--94oo0BCW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9zqj8ab4s1a5joa9umsu.gif)

这就是**“装载微光！”**

[![](img/a5a10c8684a4c601000d140218ca770c.png)](https://i.giphy.com/media/LZfZXcFNOOzw4/giphy.gif)

上面的布局由一个单独的部分组成，以表明个人资料图片，说明和标题。加载内容时可以显示的通用模板。

## 让我们学习如何在我们的应用程序中实现这一点

我们将用 CSS 和 HTML 实现完整的代码。

完整代码可在 [codepen](https://codepen.io/dhilipkmr/pen/xejEBa) 中获得

输出:

[![](img/b97e752b2acf7a32177913cc0f748849.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bytyvfcM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ee98eait0o0s7q1mq2uy.gif)

## 锅炉板块:

下面的 HTML 包括一个布局，有一个**个人资料图片**和一些**评论。**

```
<div class="card br">
   <div class="wrapper">
      <div class="profilePic animate"></div>
      <div class="comment br animate w80"></div>
      <div class="comment br animate"></div>
      <div class="comment br animate"></div>
   </div>
<div> 
```

Enter fullscreen mode Exit fullscreen mode

```
.br {
  border-radius: 8px;  
}
.w80 {
   width: 80%;
}
.card {
  border: 2px solid #fff;
  box-shadow:0px 0px 10px 0 #a9a9a9;
  padding: 30px 40px;
  width: 80%;
  margin: 50px auto;
}
.profilePic {
  height: 65px;
  width: 65px;
  border-radius: 50%;
}
.comment {
  height: 10px;
  background: #777;
  margin-top: 20px;
} 
```

Enter fullscreen mode Exit fullscreen mode

`.card`类给出卡片似的背景。

## 展开动画:

```
.wrapper {
  width: 0px;
  animation: fullView 0.5s forwards linear;
}

@keyframes fullView {
  100% {
    width: 100%;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `.wrapper`有一个自定义的**扩展**动画，我们在显示微光的同时将宽度从 0 增加到 100%。

## 微光:

```
.animate {
   animation : shimmer 2s infinite;
   background: linear-gradient(to right, #eff1f3 4%, #e2e2e2 25%, #eff1f3 36%);
   background-size: 1000px 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

`.animate`类有一个名为‘微光’的自定义动画，持续时间 2 秒，计数无限大(保持重复)。

*   `background`是一个`linear-gradient`。我们需要使用渐变，因为它与前后的其他颜色融合在一起。

*   这里我们指出从左到右有一个渐变，25%之间的颜色是`#e2e2e2`(较暗的阴影)，在所有其他地方(0 到 4%和 36%到 100%)是`#eff1f3`(实际的背景颜色)

*   `background-size`帮助提供背景的`width`和`height`。

```
@keyframes shimmer {
  0% {
    background-position: -1000px 0;
  }
  100% {
    background-position: 1000px 0;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   在我们的自定义动画中，我们定义了它在动画开始和结束时应该如何出现。
*   因此，背景的位置从其宽度的负最大值移动到正最大值。
*   自从我们使用`infinite`以来，它在每个给定的持续时间(2s)内持续发生。

不要忘记点击`follow`按钮:P

完整代码可在 [codepen](https://codepen.io/dhilipkmr/pen/xejEBa) 中获得

那都是乡亲:)