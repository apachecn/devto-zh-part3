# Firefox 66 的新特性:动画 CSS 网格

> 原文：<https://dev.to/bnevilleoneill/new-in-firefox-66-animating-css-grid-147p>

[![](img/7167ac31d0133061f59d1c652311e46c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sweyJABS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARoA9s-Y99CxbCUtuVyet9w.jpeg)

Firefox 在不断改进，Mozilla 团队设法用每个新版本给我们带来惊喜。

我们已经看到[最近在他们的开发工具上的显著改进](https://dev.to/bnevilleoneill/firefox-devtools-for-css-authors-3974-temp-slug-5932863)推动许多开发人员在开发中使用 Firefox，CSS 模块和 API 的浏览器实现也在不断增长。

最近登陆的一个功能是 CSS Grid 的动画。根据 [CSS Grid level 1 规范](https://drafts.csswg.org/css-grid/#track-sizing)，一些网格属性应该是可动画的，但是没有浏览器实现过这方面。所有的供应商都选择妥协，并在没有 Grid 的情况下发布了 Grid，所以我认为这是一个很难(或者不可能)实现的特性，我也不期望它会出现。但幸运的是，我错了。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 动画显示网格行和列

从 Firefox 66 开始，控制网格轨迹尺寸的属性现在是可动画化的。这意味着网格-模板-行和网格-模板-列可以通过适当的插值在动画中操作。

实现非常简单:与任何其他 CSS 动画一样，只需为元素定义动画，并设置一个@keyframes 规则来控制它。例如:

```
.element{
   grid-template-columns: 1fr 1fr 1fr;
   animation: resize 2000ms ease infinite alternate;
 }

 @keyframes resize {
   to {
     grid-template-columns: 1fr 2fr 1fr;
   }
 } 
```

将提供一个具有三个等宽列的网格(除非任何网格项目具有更大的固有大小… [FR 单元不是那么简单](https://github.com/w3c/csswg-drafts/issues/1777))来来回回地动画显示一个具有更宽中间列的网格。

尽管有一些限制。引用规范:

> *动画:作为长度、百分比或计算的简单列表，假设唯一的区别是列表中长度、百分比或计算组件的值*

这意味着动画只能影响不同轨迹的大小(而不是从模板中添加/移除轨迹),如果混合了单位类型，插值将不起作用。例如，一个轨道不能从 40px 到 1fr 制作动画。

这个由 [Michelle Barker](https://twitter.com/mbarker_84) 创作的例子展示了一个网格动画的基本作品(为了更好的测量，有一些颜色的变化):
[https://codepen.io/michellebarker/embed/oJmZKK?height=600&default-tab=result&embed-version=2](https://codepen.io/michellebarker/embed/oJmZKK?height=600&default-tab=result&embed-version=2)
一个需要记住的好技巧是使用空的轨道来推动实际的内容。我们可以将一个元素放置在一个特定的轨道(列/行)中，并让空的轨道产生动画，以便“推动”这个元素:

```
<div class="grid">
  <div class="element"></div>
</div> 
```

```
.grid{ 
  display: grid;
  animation: push 2s linear infinite alternate;
}

.element{
  grid-row: 2;
}

@keyframes push{
  from{
    grid-template-rows: 0fr auto;
  }
  to{
    grid-template-rows: 1fr auto;
  }
} 
```

上面的代码将把元素放在第二行，从上到下再推回来(因为第一行将从零高度增长到占据所有的空白空间)。

我最喜欢的这种技术的一个例子是安德鲁·哈佛的这支棒极了的笔，它再现了一个很棒的有弹性的 DVD 标志:
[https://codepen.io/aharvard/embed/roPvmG?height=600&default-tab=result&embed-version=2](https://codepen.io/aharvard/embed/roPvmG?height=600&default-tab=result&embed-version=2)
我用同样的技术创建了一个纯粹的 CSS 无限 Pong 演示:
[https://codepen.io/facundocorradini/embed/bJQgOB?height=600&default-tab=result&embed-version=2](https://codepen.io/facundocorradini/embed/bJQgOB?height=600&default-tab=result&embed-version=2)

### 间隙也可以制作动画

一个已经存在了相当长一段时间但仍不为大多数人所知的特性是网格间隙动画。网格-间隙、网格-列-间隙和网格-行-间隙属性也可以通过定义一个简单的@keyframesrule 来激活。这部分规范甚至拥有近乎完美的浏览器支持。

这为其他酷的效果打开了可能性。例如，我们可以分离实际的元素，或者让它们开始时相距很远，然后在中心发生冲突。

下面的代码将使两个网格项完全脱离屏幕开始，并折叠到中间。

```
.grid{
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 300px;
  animation: gap 2s;
}

@keyframes gap{
  from{
    grid-gap: 100%;
  }
  to{
    grid-gap: 0%;
  }
} 
```

Manuel Matuzovic 的这支笔展示了如何在“现实世界”中使用它(它在加载时是动画的，所以如果你错过了，请刷新):
[https://codepen.io/matuzo/embed/WOLzNN?height=600&default-tab=result&embed-version=2](https://codepen.io/matuzo/embed/WOLzNN?height=600&default-tab=result&embed-version=2)

### 关于性能的说明

即使有这些限制，动画 CSS 网格属性也可以实现令人惊叹的效果。不过，有一个警告，与任何高度/宽度变化一样，动画网格列、行或间隙会触发布局的变化，这可能会影响性能。

布局计算在[渲染管道](https://developers.google.com/web/fundamentals/performance/rendering/)的中间，这意味着浏览器将不得不在动画期间不断地重复*布局* **和**以下步骤(*绘制*和*合成*)。

[![](img/5ab108a47d4685be65f511637dd22750.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6GpnWcb1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AT7jlcg_hq86XM5yc.jpg) 

<figcaption>全像素流水线</figcaption>

通常认为只对改变合成步骤的属性进行动画制作是一种好的做法(这几乎意味着只进行不透明和变换)，所以如果你要在制作中使用 CSS 网格动画，请有目的地负责任地使用。为了最小化对性能的影响，仅动画化具有很少后代的元素，并使用简单的剪辑/堆叠上下文树。

此外，考虑使用 [will-change 属性](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)来通知浏览器哪些部分将被动画化，这样他们就可以优化它。请记住，will-change 上的多个属性应该以逗号分隔的值链接起来。所以，如果你要同时激活列和行模板，使用 will-change:grid-template-columns，grid-template-rows；。

### 其他浏览器怎么样？

Chromium 浏览器*类似于*动画 CSS 网格，但是没有插值。这意味着我们可以定义一个@keyframes 动画，但它会在步骤之间跳跃，而不是平滑过渡。想想看，将元素的可见性从隐藏变为可见是如何产生跳动的外观，而过渡不透明度是如何使其逐渐出现的。

嗯，Chromium 让网格状的可视性变得生动，而 Firefox 让它变得不透明。

但是嘿！一如既往，有了 CSS，*我们就能黑掉它*。我们可以将网格轨迹(行或列)定义为某种自动大小(自动、最小内容、最大内容),然后动画显示网格元素的宽度/高度。

```
.container{
  display: grid;
  grid-template-columns: auto 1fr;
}

.element{
  width: 0;
  animation: grow 3s ease-in-out infinite alternate;
}

@keyframes grow{
  to{
    width: 200px;
  }
} 
```

下面是一个基于上面代码的基本例子:
[https://codepen.io/facundocorradini/embed/axQrMW?height=600&default-tab=result&embed-version=2](https://codepen.io/facundocorradini/embed/axQrMW?height=600&default-tab=result&embed-version=2)
不是非常实用或高性能的，但它为一些给定的用例提供了一种插值网格动画

类似的方法可以用于基于过渡而不是关键帧的动画:
[https://codepen.io/facundocorradini/embed/VQZjdx?height=600&default-tab=result&embed-version=2](https://codepen.io/facundocorradini/embed/VQZjdx?height=600&default-tab=result&embed-version=2)

### 结论

CSS 网格动画的实现让我们可以在界面上添加惊人的效果。对性能的影响和缺乏其他更流行的浏览器的支持可能意味着我们不会经常看到这一点，但 Firefox 团队为开发人员和设计人员提供了一个很好的实验工具，为出色的 CSS 效果和工具铺平了道路。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

火狐 66 中的新帖子[:动画 CSS 网格](https://blog.logrocket.com/new-in-firefox-66-animating-css-grid-b4ed90ac32f5/)首先出现在[的日志博客](https://blog.logrocket.com)上。