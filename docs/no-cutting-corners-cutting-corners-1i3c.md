# 禁止偷工减料偷工减料

> 原文：<https://dev.to/pulljosh/no-cutting-corners-cutting-corners-1i3c>

编程最困难的方面之一是预测一个特定的任务需要多长时间。有时候，你认为需要几周时间的事情却需要几个小时:

> ![Ben Halpern 🤗 profile image](img/3814cc9cbc453345725bf2b5e578e8a0.png)本·哈尔彭🤗@ bendhalpen![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)昨天我宣布文章浏览量将在一两周内对所有人开放。几个月来，它一直生活在脆弱的测试版炼狱中，所以一两个星期似乎是雄心勃勃的。结果只花了几个小时。
> 
> [【dev.to/devteam/change…】](https://t.co/VwJ57Tg27l)2018 年 11 月 14 日下午 16:21[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1062742360382230531)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1062742360382230531)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1062742360382230531)

但是有时候...哦，有时候，估计会变得非常非常黑暗。这是一个悲惨故事。

## 问题

在整合我的一个特殊的“秘密”项目的过程中，我发现自己创建了一个 CodeMirror 主题。对于那些不知道的人来说， [CodeMirror](https://codemirror.net/) 是一个用于在网络上创建代码输入的 Javascript 库。它被非常好地组合在一起，并被 Github、Codepen、Glitch 和[以及更多的](https://codemirror.net/doc/realworld.html)所使用。

在花了很长时间找到一些漂亮的文本颜色(很难估计)后，我开始设计文本选择。默认情况下，文本选择样式为...好吧。不过，它非常方方正正，一点也不优雅。

如果说我作为一名开发人员在做设计时学到了什么，那就是增加一点边框半径可以改善任何矩形。因此，就像我是一个优秀的、注重细节的设计师一样，我开始实现一个简单得可笑的目标:**在 CodeMirror 中为文本选择添加一个小的边框半径。**

多年来，我一直在画矩形，我知道这是一个简单的任务。30 秒。最多了。 <sup id="fnref1">[1](#fn1)</sup>

## 解

```
/* This code is infallible. */
.CodeMirror-selected {
  border-radius: 3px;
} 
```

Enter fullscreen mode Exit fullscreen mode

我写了代码，用深深满足的机械点击砸了 ctrl-s，看着 webpack 编译( *frontend 疯了，嗯？*)，并等待一些热重装的完美。

[![This radius isn't quite right.](img/fa54cb1c0ec3fd2038bbf50e0f9d0617.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J1ZfDXWS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nfr7vamz0acp33ea0to4.png)

哎呀。如果你没看到，请允许我解释清楚:

[![The most upsetting border radius on the face of the earth.](img/a6a632cc42cf5757a28cc26b8d6b4b22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wFfBQMgF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zh6wq37wtflvctjdv5mu.png)

看着它！ ***哦，恐怖中的恐怖！*** 这个选区不仅仅是一个矩形...是*三*！

我不能忍受这样令人厌恶的半径，所以我开始寻找解决办法。

## 解

几分钟后，我明白了这个问题其实并不难。有三个独立的矩形，所以可以有三组独立的样式。

对于第一个矩形，顶角有半径，但底角没有:

```
.CodeMirror-selected:nth-of-type(1) {
  border-top-left-radius: 3px;
  border-top-right-radius: 3px;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后是中间的矩形。原来在标记中，中间的是第三，所以会是`:nth-of-type(3)` :

```
.CodeMirror-selected:nth-of-type(3) {
  border-top-left-radius: 3px;
  border-bottom-right-radius: 3px;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在是底部的矩形，底部的角应该是圆形的:

```
.CodeMirror-selected:nth-of-type(2) {
  border-bottom-left-radius: 3px;
  border-bottom-right-radius: 3px;
} 
```

Enter fullscreen mode Exit fullscreen mode

而且，瞧，它是完美的！

[![Look at that beauty.](img/56d83b20943502d6cc5d72568f94c2d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ug9ShAuU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/irnedoveue3b4o4d40zk.png)

...开玩笑的。

[![](img/6a2a2f141aa9f3bb364965624e537a5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hPnoPe3A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vrmvz05oudg33xl4s1eh.png)

如果您选择任何其他形状，这种方法就不起作用，并且没有办法检测选择的形状，所以我们又回到了起点。

但是我不会放弃的！毕竟，我没有(恰当地)抄近路。

## 解

主要的问题是，我的解决方案没有很好的环境意识。我盲目地应用了边界半径 <sup id="fnref2">[2](#fn2)</sup> ，而我真正想要的是一组自适应的矩形，它们可以平滑地融合在一起。

作为一名 17 岁的痤疮患者，我已经看够了一些相当明智的编程智慧。这里有一条事后看来总是显而易见的修复 bug 的建议:**编写描述你想要做的事情的代码。**

写一些与你的目标无关的代码是很诱人的；作为所写内容的副作用而达到目的的代码。但是干净的代码是清晰和直接的。

那么这里的直接解决方案是什么呢？好吧，与其设置单独的边框半径，不如写一些 CSS 直接说，“让盒子互相粘合。”

你知道什么是疯狂吗？解决方案是存在的。

如果我们向页面添加一个 SVG 过滤器定义:

```
<!-- https://css-tricks.com/gooey-effect/ -->

  <defs>
    <filter id="selection-goo">
      <feGaussianBlur in="SourceGraphic" stdDeviation="1" result="blur"></feGaussianBlur>
      <feColorMatrix in="blur" mode="matrix" values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -9" result="goo"></feColorMatrix>
      <feComposite in="SourceGraphic" in2="goo" operator="atop"></feComposite>
    </filter>
  </defs>
 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在选择`div` s 的父对象上使用它(与`filter: url(#selection-goo)`),它将导致三个独立的框以我们想要的方式彼此靠近:

[![Finally, a working solution! For real this time.](img/0645d794afc69e5b62d84966227e8a06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O9uiy13U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/786vdczxg7rtjwlqlv78.png)

看看吧！即使是多选，也很好用。它也为我们在内角做了一点反效果，看起来很整洁！(写代码说明我们想要实现什么——黏糊糊的盒子——而不是关于如何实现它的无关紧要的细节，这两个都是好的副作用。)

## 结论

我们有解决办法。它工作了。真的。没抓到你。不偷工减料(除了我们想要的)。这可能是人生的一课，尽管我不确定到底是什么。

这个解决方案是个好主意吗？绝对不行。对于一个不需要解决的问题，这种解决方案的效率低得惊人。应用 CSS 滤镜占用了相当大一块 CPU (GPU？)本可以花在更好的事情上的时间。

我到底要不要用它？当然啦！我是一名网页开发人员，现在是 2018 年！ <sup id="fnref3">[3](#fn3)</sup>

* * *

1.  我向那些经历了 html 表格时代并使用图像创建边界的人致以最诚挚的慰问？).还有，你现在老了。 [↩](#fnref1)

2.  “半径”无论我选哪个复数，100%的评论都会不爽。互联网的迂腐没有界限。 [↩](#fnref2)

3.  请派人来帮忙。 [↩](#fnref3)