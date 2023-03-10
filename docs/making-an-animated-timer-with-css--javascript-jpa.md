# 用 CSS 和 JavaScript 制作一个动画定时器

> 原文：<https://dev.to/nikacodes/making-an-animated-timer-with-css--javascript-jpa>

随着我在大会上的时间迅速接近尾声，直接的指导越来越少，而更多的是鼓励我们在 web 开发这个巨大的沙箱中玩耍。最近，我们花了一整天的时间自学 GA 课程之外的另一种语言或技能。

我选择用 CSS 动画创建一个 JavaScript 定时器。

## 我为什么选择这个科目？

我对我的顶点项目有一个想法，通过 JavaScript 触发的 CSS 动画将极大地增强视觉效果。

## 它解决什么问题？/为什么要使用它？

它并没有解决太多的问题，但有可能使一个现有的项目更美观地取悦用户。

视觉愉悦使用户界面更容易理解和导航，也更容易卖给潜在买家或用户。

## 有哪些替代品？

我将很少或没有样式或静态网页，其中唯一移动的部分是 JavaScript 计时器，这可能看起来相当不吸引人或无聊。

# 教导自己

## 第一步——制作计时器。

幸运的是，互联网——在其巨大的信息财富中——有许多 JavaScript 计时器的例子。我用了一个来自 w3schools 的(从 1 分钟开始倒数)。

***注意**:我还让它在计时器到达 0 后 1 分钟重启，以确保动画在一次页面加载中不止一次工作。*

## 第二步——添加基本的 CSS 样式。

确定我的倒计时器的静态版本是什么样子，并定义任何必要的类。(这很容易。)

## 第三步——了解动画。

**动画与过渡**

一个**转换**被应用于一个元素，并指定一个在一段时间内逐渐发生的变化。

一个**动画**只是在后台运行，不管通过用户或网站的其他部分发生了什么。

此时我意识到这整个项目的标题(*用 CSS & JavaScript* 制作一个动画定时器)是个谎言。我实际上是在用 CSS & JavaScript 制作一个*过渡计时器*。

## 第四步——添加独立于 JS 的动画

我可以在 CSS 代码中添加一个 div，它会在指定的时间内收缩。

```
.visual-timer {
    border-top: 50px solid #004D00;
    margin: 5% auto;
    width: 50%;
}

.width-change {
    /* Animation code */
    -webkit-animation-name: widthChange; /* Safari 4.0 - 8.0 */
    -webkit-animation-duration: 60s; /* Safari 4.0 - 8.0 */
    animation-name: widthChange;
    animation-duration: 60s;
}

@keyframes widthChange {
    from {width: 600px;}
    to {width: 0px;}
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第五步——连接动画的开始与 JS 函数的开始

通过在函数的开头添加`visualTimer.classList.add("width-change");`,我能够在定时器开始倒计时时启动动画。然而，它只会工作一次，然后当计时器重置时不会重新启动。

这很容易通过在计时器重置时添加`visualTimer.classList.remove("width-change");`来修复，允许函数在循环时重新初始化动画。

## 在研究这个问题时，你遇到的最大的概念障碍(如果有的话)是什么？

在这一点上，我仍然对动画没有准确反映计时器感到不满意。这是不一致的，所以当计时器在 30 秒时，倒计时已经过半。

但后来我找到了另一种方法来分解动画，使它不那么快。

```
@keyframes widthChange {
    0%   {width: 600px;}
    25%  {width: 450px;}
    50%  {width: 300px;}
    75%  {width: 150px;}
    100% {width: 0px;}
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，当计时器*为*30 秒时，它就达到了动画长度的一半。然而，宽度变化的速度仍然不像我希望的那样一致。

## 哪些文章或论坛对你的学习最有帮助？

[https://CSS-tricks . com/controlling-CSS-animations-transitions-JavaScript/](https://css-tricks.com/controlling-css-animations-transitions-javascript/)
T3】https://www . site point . com/css3-animation-JavaScript-event-handlers/
[https://www.w3schools.com/css/css3_animations.asp](https://www.w3schools.com/css/css3_animations.asp)
[https://www.w3schools.com/howto/howto_js_add_class.asp](https://www.w3schools.com/howto/howto_js_add_class.asp)
[https://gomake things . com/two-way-to-set-an-elements-CSS-with-vanilla-JavaScript/](https://gomakethings.com/two-ways-to-set-an-elements-css-with-vanilla-javascript/)

## 为未来

除了更一致的动画，我想有一个基于计时器的动画持续时间的变化。因此，如果计时器更长或更短，动画将加速或减速以匹配。

我想到的可能的解决方案是集成 SASS 或在 JavaScript 本身中构建样式，并使持续时间成为变量。如果谁有什么建议，欢迎！

## 为 CodePen 的修改版本

[https://codepen.io/sun-mountain/embed/zbYNOJ?height=600&default-tab=result&embed-version=2](https://codepen.io/sun-mountain/embed/zbYNOJ?height=600&default-tab=result&embed-version=2)