# 弯曲你的肌肉💪使用 CSS Flexbox

> 原文：<https://dev.to/ogwurujohnson/flexing-your-muscles-with-css-flexbox-3p8f>

[https://api.parler.io/ss/player?url=https://www.parler.io/audio/19721053239/0232126ad1378dc918da08e7cfdb79647a3f02e7.b7e54476-aae8-47b2-bee4-103e546a13d4.mp3](https://api.parler.io/ss/player?url=https://www.parler.io/audio/19721053239/0232126ad1378dc918da08e7cfdb79647a3f02e7.b7e54476-aae8-47b2-bee4-103e546a13d4.mp3)

**免责声明:**

> 说真的，谁会以免责声明开始一篇文章呢？嗯，我。我连保命的 CSS 都写不出来。这是我在 lambda 学校的第二周，一切都很棒。在此之前，我对 CSS 有这种恐惧，因为它太难了😭多亏了 lambda 学校，恐惧现在成为了一种灵感💪😊。

首先，请记住，我们将把你对 CSS 的恐惧转化为激励因素，促使你想在 CSS 方面做得更好💪。

```
.johnson-div {
    display: flex;
 } 
```

Enter fullscreen mode Exit fullscreen mode

哦，是啊。上面的代码是你开始使用 flex 所需要的全部，但不是让你的站点充分利用 flex 的优势和能力所需要的全部。为了更好地理解所讨论的概念，我们将利用一些难看的盒子的图片。在跳转到 [Flex 属性部分](##Flex%20Properties)之前，好好看看下图

[![div explanation](img/47a2a6997ab39f2885125b499483740d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GmxzGBBm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jcjju9xdocfy2x2c2g5w.png)

## 伸缩属性:

***- Display:*** 这定义了一个 flex 容器，我们在前面的代码片段中已经看到了。

```
.johnson-div {
    display: flex;
} 
```

Enter fullscreen mode Exit fullscreen mode

***- Flex-direction:*** 该属性用于指定我们希望我们的`container-items`流动的方向以及它们的方向应该是什么样子。柔性方向可以具有值`column`、`column-reverse`、`row-reverse`、`row`，这是默认的柔性方向。下面的图片描述了每一个是如何工作的。

```
.johnson-div {
    display: flex;
    flex-direction: row || row-reverse || column || column-reverse;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![row and column](img/a00c25de32f89a750c9de0ed986240b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FVMuSb7M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ccrh5xnu41jmh7b4jqpl.png)
[![row-reverse and column-reverse](img/9b5377f8e87c1b4e7481cbf1780feb85.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--C2gfFXJF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k6fjiho7vcfiij18ab3b.png)

***-Justify-content:***这个属性帮助我们在容器项中沿着主轴分布空间，现在问题来了，主轴是什么。为了回答这些问题，对于 flex 中的容器，我们有了`main-axis`和`cross-axis`。

*   对于`flex-direction: row`，我们的`main-axis`从左向右跑，而在`flex-direction: column`上，我们的`main-axis`从上到下跑。
*   对于`flex-direction: row`，我们的`cross-axis`从上到下跑，而在`flex-direction: column`上，我们的`cross-axis`从左到右跑。

下图进一步描述了这些概念:

[![main axis and cross axis](img/95754646b476d822bf751c098da322ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UMwkBXMc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/loudjvo0kvfi46o62u4o.png)

解释完主轴和横轴是什么，让我们回到讨论`justify-content`。
`justify-content`取值为，间距、均匀间距、周围间距、伸缩结束、伸缩开始、居中。下面的图片展示了这个属性对我们的容器项目的影响。

```
.johnson-div {
     display: flex;
     flex-direction: row || row-reverse || column || column-reverse;
     justify-content: space-between || space-around || center || space-evenly ||flex-start || flex-end;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![space-between, space-evenly](img/14fac1091a7ee1786b3d7c09ca0fb6cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H18nf10n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3pxge9orfi3yrchci40p.png)
[![space-around, flex-end](img/e3ffbc756bab7b82c04e551e1db2215f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g6e_l3hE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bznn3y1sjt5przfgw74w.png)
[![flex-start, center](img/fc0d4521978f5e7fef6deb4caa525ce3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9RKgABwt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zjsmeqzobugkirryfgzh.png)

以下是使用图表说明的数值的口头解释:

*   `flex-start(default)` : *集装箱物品朝向起跑线*放置。
*   `flex-end` : *集装箱物品朝向终点*放置。
*   `center` : *集装箱物品沿*线居中。
*   `space-between` : *分配容器项目，第一个项目在起始行，最后一个项目在结束行。这里的空间是不平等的*。
*   `space-evenly` : *分配容器项目，使得任意两个容器项目之间的间距相等*。

***- Align-items:*** 这个属性就像`justify-content`一样，帮助我们分配空间但是这次是沿着横轴。它也像`justify-content`一样有自己的价值观，有些是；

```
.johnson-div {
     display: flex;
     flex-direction: row || row-reverse || column || column-reverse;
     justify-content: space-between || space-around || center || space-evenly ||flex-start || flex-end;
     align-items: flex-start || flex-end || center || baseline || stretch;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `flex-start` : *做同样的事情，但是这次是在横轴上。*
*   `flex-end` : *做同样的事情，但是这次是在横轴上。*
*   `center` : *做同样的事情，但是这次是在横轴上。*
*   `baseline` : *容器项目被对齐，使得它们的基线对齐。*
*   `stretch(default)` : *拉伸填充容器。*

[![stretch](img/edcb8cb2dc437469febe4af67b88d944.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mhylldWB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x6vf17s0qsoeteouogcf.png) 。

使用`justify-content`和`align-items`，只需使两者的值居中，就可以轻松地将容器或 div 在网页上居中。

***- Flex-wrap:*** 默认情况下，Flex 项目都希望停留在一行上，这导致项目被缩小以便适应屏幕，这有时看起来真的很丑。
但是为了解决这个问题，并在到达行尾时强制我们的项目流入其他行，我们使用了另一个 flex 属性`flex-wrap`。
`flex-wrap`，有值:`no-wrap`、`wrap`、`wrap-reverse`。下图说明了每种方法的用法。

```
.johnson-div {
     display: flex;
     flex-direction: row || row-reverse || column || column-reverse;
     justify-content: space-between || space-around || center || space-evenly ||flex-start || flex-end;
     align-items: flex-start || flex-end || center || baseline || stretch;
     flex-wrap: wrap || nowrap || wrap-reverse;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![flex-wrap images](img/dcad40f89dcd435485154445799d52b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4RQF6IBS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/12zjmyntc0kt3feivhja.png)

> 注意:把`flex-direction`和`flex-wrap`两者结合起来有一个快捷方式，叫做`flex-flow`。当我们使用代码时，请注意它的变化。

```
.johnson-div {
     display: flex;
     justify-content: space-between || space-around || center || space-evenly ||flex-start || flex-end;
     align-items: flex-start || flex-end || center || baseline || stretch;
     flex-flow: row wrap || column nowrap || row wrap-reverse || etc;
} 
```

Enter fullscreen mode Exit fullscreen mode

> 最后，flex 还有其他属性，你可以直接应用到 flex 项目中，我允许你研究这些。但是如果你不能，我发现这篇来自 [CSS 窍门](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)的文章很有用。

我希望你也能发现我的文章有用😊，分享并留下我对文章的改进意见。谢谢