# 100 天代码的第 1-5 天

> 原文：<https://dev.to/misnina/day-1-5-of-100-days-of-code-3jmk>

本周一开始 100 天代码！我没有完成我想要的那么多，因为我有点不舒服，但我刚刚完成了我的第二个迷你项目。我同时开始了他们两个，但是我的第一个项目是互动卡！

[![](img/0345de3cde5487c50bc0def5b540a910.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cplDU_9l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cp6ii830jl3qoc1oql6w.gif)

我在 [Codier](https://codier.io/) 上看到一个关于制作未来卡片的挑战，但是我决定在 react 中制作我自己类型的卡片。我承认 CSS 动画对我来说有点陌生！我需要更多地研究和使用它们。它没有帮助，我一头扎进它做三维透视！

然而，我不得不说，到目前为止，一个失败是我没有使它移动响应。当我把它变小的时候，宽度破坏了布局，所以我不得不为手机重新设计整个东西，这并不坏，只是意味着这是另一天的另一个项目。

```
 #scene {
  width: 35rem;
  height: 20rem;
  perspective: 600px;

  #card {
    width: 100%;
    height: 100%;
    position: relative;
    transform-style: preserve-3d;
    transition: transform 1s;
  }

  .back {
    transform: rotateY(180deg) rotateX(0deg);
    backface-visibility: hidden;
  }

  .front {
    transform: rotateX(0deg);
    position: absolute;
    height: 100%;
    width: 100%;
  }
}

.isFlipped {
  transform: rotateY(-180deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

我做的第二个项目实际上有一个[页面](https://misnina.github.io/pokemon-mail/)你可以访问！使用《口袋妖怪》中的黑白资源，以及各种口袋妖怪主题字体，我制作了一个小应用程序，让你将一个 [html 元素保存为一条可爱消息的图像](https://github.com/tsayen/dom-to-image)。

[![](img/8822d4be58ef3a2a35ef0a976596995f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wJC5AthF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1n7376h975rsdsm4nlbk.gif)

这是一个小应用程序，但它教会了我更多关于反应状态和状态管理的知识。我还没有进入 Redux，因为它一直在我的脑海里，但我有一天会到达那里。

```
 grabMail() {
    domtoimage.toPng(document.getElementById('mail'))
      .then(dataURL => {
        download(dataURL, 'mail.png');
      })
  } 
```

Enter fullscreen mode Exit fullscreen mode