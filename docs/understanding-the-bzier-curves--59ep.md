# 理解贝塞尔曲线🙈

> 原文：<https://dev.to/wgao19/understanding-the-bzier-curves--59ep>

*照片:[福柯摆](https://en.wikipedia.org/wiki/Foucault_pendulum)在[华盛顿大学](http://pcparch.com/project/physics-and-astronomy-building)物理天文楼*

# 直觉

省略构造细节，三次 Bézier 曲线是至多具有两个平滑转弯的[0，1]曲线。

使用 CSS，三次贝塞尔曲线由四个点定义。第一个点是`(0,0)`，最后一个点是`(1,1)`，你只需要供给第二个`(x1, y1)`和第三个`(x2, y2)`点:

```
cubic-bezier(x1, y1, x2, y2) /* you drop the French accent mark, too */ 
```

你可以在这个网站上玩三次贝塞尔曲线。

直观地说，可以这样想象一条三次贝塞尔曲线:你离开原点`(0, 0)`向第二个点的方向，向第三个点弯曲后到达终点`(1, 1)`。我把中间的平滑转弯看作是两个力的结合点。

[![](img/bf64f0cb9bdd13f3c1c7e24ccaddc6e8.png)](//i.imgur.com/DeNlZZA.png)

# 线性、二次和三次贝塞尔曲线

贝塞尔曲线有一些有趣的数学特征。

如果你把两个“自由”点粘在一起，你会得到一个较低的贝塞尔曲线——你少转了一圈。所以从三次贝塞尔曲线上去掉一个点，就得到二次贝塞尔曲线。再放下一个，就得到线性曲线。

下面是 [`cubic-bezier(.23, .86, .23, .86)`](http://cubic-bezier.com/#.23,.86,.23,.86) :

[![Joining the second and the third point of a cubic Bézier curve gives you a quadratic Bézier curve, one with only one turn](img/abeda61ad38c8b9429b2770668f5ab16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BLuQ8khp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ceh3fxK.png)

[`cubic-bezier(0, 0, 1, 1)`](http://cubic-bezier.com/#0,0,1,1) :

[![](img/5a4cd4193785969496d95bd5af811ce2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M1jhqwpf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/BeTWJYb.png)

虽然标准 CSS 缓动函数允许使用*三次*贝塞尔曲线，但它们不一定要达到第三阶。

## CSS 的动画计时功能

`linear`是平凡的“一阶”线性贝塞尔曲线。

`ease-in`和`ease-out`都是二次的。这可以从数字上看出来。`ease-out`、`cubic-bezier(0, 0, .58, 1)`有第二个点(记住你不需要定义第一个点，所以`0, 0`定义了*第二个*点)与起点连接。并且`ease-in`、`cubic-bezier(.42, 0, 1, 1)`具有与端点相连的第三点。正如你在图表中所看到的，你只转了一圈。

[![`ease-in` and `ease-out`](img/ee5ffedcc51509f906866cffc797b686.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nB7eiP7C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/SbdazVM.png)

许多物理现象已经可以用二次贝塞尔曲线来表达了。例如，项目拖放就是简单的“淡入”。我还没有计算出标准`ease-in`是否与地球引力相匹配——应该有一个星球与之相匹配。

为了更好地可视化`ease-in`和`ease-out`，请查看这些[材质设计的插图](https://material.io/design/motion/speed.html#easing)。请看一看那边的*加速缓和*。这说明了现场的情况。

`ease`和`ease-in-out`是立方的。这两条曲线都有两个转弯。钟摆是“放松的”。你向底部移动越来越快，向两端移动越来越慢。

[![`ease` and `ease-in-out`](img/eb4d0f5bf7e83db2500e6436f8d59302.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jaCkVMEn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/1OYiO3R.png)

知道何时需要三次贝塞尔曲线的一种方法是当你找到两个力时。将物体抛向天空。你施加一个初始投掷力，这提供了你的第一个中间点的方向。与此同时，由于重力不断地将你的物体拉向地面，所以在停在你想要的位置或撞到地面之前，轨迹会向第二个中间点弯曲。

现在，创建看起来真实的动画应该变得非常简单了。想想施加了什么力。用你的贝塞尔曲线想象它，并应用到`translate`。

有时候我们会把现实简化一点，假设物体只是被给了一个初速度。就像一条信息从下面飞进来，停在你的眼前。很钝，没有投掷。就这么发生了。一个`ease-out`没什么大问题，但我猜我们的潜意识会喜欢这个`ease`。

# 练习

所以我试图对这个设计中的动画进行逆向工程:

[![](img/f8d83374ee8c10a02ce2e0a58dd087c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K4hnkHe6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.dribbble.com/users/501822/screenshots/4941238/dribbble_1__1_.gif)

## 摆

乍一看，它像一个`ease-in-out`，像一个钟摆。无摩擦的钟摆是对称的。我应该可以用一个无限循环的可逆二次函数来创建它。我可以选择任何一种方式，向上或向下，反转它会给出另一个方向。

```
@keyframes one-way-pendulum {
  0% {
    transform: translateY(0);
  }
  100% {
    transform: translateY(1500px);
  }
}

.card {
  animation: one-way-pendulum 1s cubic-bezier(0.6, 0, 1, 1) infinite;
  animation-direction: alternate;
} 
```

有用！但是等等，它没有暂停！
[https://codepen.io/wgao19/embed/pqMdJe?height=600&default-tab=result&embed-version=2](https://codepen.io/wgao19/embed/pqMdJe?height=600&default-tab=result&embed-version=2)

## 暂停摆

CSS 动画的循环方案没有提供一个太明显的方法来实现迭代之间的暂停。您必须通过组合循环来实现 gap。参见[本次堆栈溢出讨论](https://stackoverflow.com/questions/13887889/css-animation-delay-in-repeating)。

这意味着我的动画必须包括钟摆的两种方式。上上下下。所以二次贝塞尔曲线是不够的。我真的需要立方的。

```
@keyframes pausing-pendulum {
  0%,
  20% {
    transform: translateY(0);
  }
  40%,
  50% {
    transform: translateY(1500px);
  }
  70%,
  100% {
    transform: translateY(0);
  }
}

.card {
  animation: pausing-pendulum 4s cubic-bezier(0.5, 0, 0.5, 1) infinite;
} 
```

现在它在迭代之间很好地暂停。
[https://codepen.io/wgao19/embed/VqorvR?height=600&default-tab=result&embed-version=2](https://codepen.io/wgao19/embed/VqorvR?height=600&default-tab=result&embed-version=2)

## 哪条贝塞尔曲线更快？

三卡动画看起来很酷，一个接一个的掉。对我来说，它们看起来像是同时在运动，但是重力不同。主卡启动最慢，第二张卡稍快，而第三张卡最快。它们也以同样的反向速度返回。

所以第一台 perpetuo 应该还能用。我需要做的就是给其他的卡片一个更剧烈的贝塞尔曲线。

```
animation: pendulum 1s cubic-bezier(0.6, 0, 1, 1) infinite; /* the front most card */
animation: pendulum 1s cubic-bezier(0.3, 0, 1, 1) infinite; /* this one is faster */
animation: pendulum 1s cubic-bezier(0.1, 0, 1, 1) infinite; /* fastest of the three */ 
```

请注意，`.3`曲线比`.6`曲线快。`.6`曲线弯曲得更厉害，这意味着它减速得更快。

[![](img/12c284f1c269662e66d316053041cbd7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F1zuyHXZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/F8Ls5cd.png)

现在我们有了那三张永久牌..
[https://codepen.io/wgao19/embed/WLVXXp?height=600&default-tab=result&embed-version=2](https://codepen.io/wgao19/embed/WLVXXp?height=600&default-tab=result&embed-version=2)

## 🐛🐛🐛

要复制三张牌的停顿，你只需玩数字。EZPZ 现在🐒。再次注意，不太剧烈的曲线具有较快的初始下降速度。

[![](img/4dc0899a03d5fdf54a30616145f47b30.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OIBdjdJt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/tPt3MIm.png)T3】

```
animation: pausing-pendulum 4s cubic-bezier(0.5, 0, 0.5, 1) infinite; /* main card */
animation: pausing-pendulum 4s cubic-bezier(0.5, 0.1, 0.5, 0.9) infinite; /*  faster */
animation: pausing-pendulum 4s cubic-bezier(0.5, 0.2, 0.5, 0.8) infinite; /*  fastest */ 
```

[CodePen 链接](https://codepen.io/wgao19/pen/BvXmxQ)
[https://codepen.io/wgao19/embed/BvXmxQ?height=600&default-tab=result&embed-version=2](https://codepen.io/wgao19/embed/BvXmxQ?height=600&default-tab=result&embed-version=2)

# 参考文献

*   [三次贝塞尔曲线](http://cubic-bezier.com)
*   [贝塞尔曲线-维基百科](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)
*   滴水不漏的设计:[一个极简的节日主页备选版动画](https://dribbble.com/shots/4941238-One-Minimal-Festival-Homepage-Alternative-Version-Animation)
*   [CSS 动画延迟重复](https://stackoverflow.com/questions/13887889/css-animation-delay-in-repeating)
*   [福柯摆](https://en.wikipedia.org/wiki/Foucault_pendulum)