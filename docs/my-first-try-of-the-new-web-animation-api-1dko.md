# 我第一次尝试新的网络动画 API

> 原文：<https://dev.to/snowleo208/my-first-try-of-the-new-web-animation-api-1dko>

最近，我(终于)发现了新的 Web 动画 API，它可以像 CSS one 一样使用 JavaScript 创建流畅的动画，但具有更多功能，如改变速度或播放/暂停/取消动画。开发者可以更灵活地创建不同动画。

> 注意:web 动画 API 仍然是一项实验性的技术，并不是所有的浏览器都支持它。使用前不要忘记添加 [polyfill](https://github.com/web-animations/web-animations-js) ！

* * *

## 初次尝试

这一次，我试图创建齿轮的动画，因为看到不同速度的齿轮是正常的，或者用户可以在需要时选择开始/停止动画。

通常，您可以使用 CSS 创建旋转齿轮，如下所示:

```
.gear1 {
    animation: gears 1500ms infinite;
}

@keyframes gears {
    0% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(360deg);
    }
} 
```

但是现在，您可以使用几行 Javascript 来创建相同的效果:

```
 const rotate = [
    { transform: "rotate(0deg)" },
    { transform: "rotate(360deg)" }
  ];

  const timing = {
    duration: 1500,
    iterations: Infinity
  };

  const firstGear = document.getElementById("gear1").animate(rotate, timing); 
```

你可以看到`@keyframes`上的设置被移到了 animate()的第一部分，与 CSS 不同的一点是`infinite`在新的网页动画 API (WAAPI)中对`iterations: Infinity`的改变。

使用 WAAPI 的第一个动画:

[https://codepen.io/snowleo208/embed/QzqbZW?height=600&default-tab=js,result&embed-version=2](https://codepen.io/snowleo208/embed/QzqbZW?height=600&default-tab=js,result&embed-version=2)

你也可以像 CSS 一样控制动画中的百分比:

```
const blink = [
  { transform: "rotate(0deg)", opacity: 1, offset: 0 },
  { opacity: 0, offset: 0.3 },
  { opacity: 1, offset: 0.5 },
  { opacity: 0, offset: 0.7 },
  { opacity: 1, offset: 0.9 },
  { transform: "rotate(360deg)", opacity: 1, offset: 1 }
];

  const timing = {
    duration: 1500,
    iterations: Infinity
  };

const firstGear = document.getElementById("gear1").animate(blink, timing); 
```

正如你所看到的，`offset: 0`就像 CSS 的关键帧中的`0%`,你可以改变不同的属性，如颜色，变换，不透明度等。如你所愿。

对于时间，你也可以添加其他设置，如延迟，缓解，填充等。您可以在此查看完整列表[。](https://developer.mozilla.org/en-US/docs/Web/API/Element/animate)

* * *

## 控制动画

创建齿轮后，您还可以使用 Javascript 控制动画。像我们在第一部分所做的一样，我们将 gear 元素声明为`firstGear`，现在我们可以用它来打开或关闭动画。

例如，你可以创建一个按钮，当用户点击它时，它将停止动画。

```
<button id="stop">Stop</button> 
```

然后，只需添加一行 javascript，就大功告成了:

```
 document.getElementById("stop").addEventListener("click", function() {
    firstGear.pause();
}); 
```

您也可以使用这些来开始或取消动画:

```
firstGear.start();
firstGear.pause();
firstGear.cancel();
firstGear.reverse(); 
```

对于反转，只有正常动画可以使用反转。如果你正在创建像这个齿轮一样的无限动画，你不能简单地使用`<YOUR-ELEMENT>.reverse()`。

在这种情况下，我们可以在定时变量中使用`direction`:

```
const timing = {
    duration: 1500,
    direction: "reverse",
    iterations: Infinity
  }; 
```

[https://codepen.io/snowleo208/embed/ebGNjd?height=600&default-tab=js,result&embed-version=2](https://codepen.io/snowleo208/embed/ebGNjd?height=600&default-tab=js,result&embed-version=2)

* * *

## 速度为王！

我们如何在一些事件后改变动画速度(例如点击按钮后)？

```
 document.getElementById('<YOUR-ELEMENT>').addEventListener("click", function() {
    firstGear.playbackRate = 2; //speed x 2
    firstGear.playbackRate = 1; //normal speed
    firstGear.playbackRate = -1; //reverse your animation!
}); 
```

如果你愿意，你甚至可以在里面设置一些条件！比如加载动画，一开始档位可以移动的很慢，但是页面快加载的时候档位速度可以快一点！

在这里查看我的完整齿轮:
[https://codepen.io/snowleo208/embed/jXLowg?height=600&default-tab=js,result&embed-version=2](https://codepen.io/snowleo208/embed/jXLowg?height=600&default-tab=js,result&embed-version=2)

* * *

## 还有更？

不幸的是，现在对 WAAPI [的支持不是很好](https://caniuse.com/#feat=web-animation)，一些功能在当前的浏览器中不被支持。

例如，如果你需要改变动画，比如从旋转效果切换到淡入淡出效果。您将需要使用`composite`和`iterationComposite`属性，以及 setKeyframes()函数。遗憾的是，现代浏览器并不完全支持这些功能。

尽管如此，我们仍然能够使用基本功能创建带有更多控件的流畅动画。希望以后浏览器能支持越来越多的功能！

* * *

## 阅读更多

*   [使用网络动画 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API)
*   [CSS 动画 vs 网页动画 API](https://css-tricks.com/css-animations-vs-web-animations-api/)
*   [网络动画 API 的附加动画](https://css-tricks.com/additive-animation-web-animations-api/)