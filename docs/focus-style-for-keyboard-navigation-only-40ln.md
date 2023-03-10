# 仅用于键盘导航的焦点样式

> 原文：<https://dev.to/jeromesm/focus-style-for-keyboard-navigation-only-40ln>

*读完这篇文章后，我有了写这篇文章的想法[为更好的可用性设计按钮焦点状态](https://dev.to/elizabethschafer/designing-button-focus-states-for-better-usability-gm2)，如果你还没有读过，那就去读吧。*

## 可访问性 vs UI

*嗯，我并不是真的想把 a11y 反对 UI 作为一场战斗但是想不出更好的名字。*

有时，您可能希望删除浏览器将免费提供的默认轮廓。当你这样做的时候，你肯定会让你的设计师高兴，但是你会让那些依赖辅助技术或者键盘导航的人感到困难。

有了`:focus-visible` ( [规格在工作草案中](https://drafts.csswg.org/selectors-4/#the-focus-visible-pseudo))你可以让双方都满意。我仍然不建议删除应用程序每个元素的整个轮廓，因为它仍然可以让一些用户受益和帮助他们。

尽管在某些情况下，焦点环在你的用户界面中可能不太好，但我能想到的是可点击的图标，例如箭头← →分页，打开菜单的汉堡图标。

[![Example of a click on a hamburger icon to open a menu which also shows the native outline focus ring](img/b66f394ec81c78e181ff61fdfc059d06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2DMbiUac--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3jor5g4faqcnnlqvzqh7.gif)

此外，在自定义样式`<button>`具有高`border-radius`的情况下，`outline`不会跟随圆角，并将导致以下结果:

[![button with native outline when focused](img/8754696782011edc91af08eed865993f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ibE2c-8o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8a4bqfjcandkriw5tey2.png)

**当用鼠标点击或用手指轻击**时，这个对焦环的附加值很低(`:hover`和`:active`可能是更好的候选)，它甚至给 UI 增加了无用的噪音。另一方面，当浏览网站时，用户需要知道焦点在汉堡包图标上，这样他们才能打开菜单。

## :焦点——救援可见

两个`:focus`和`:focus-visible`的主要区别在于，你可以使用
`:focus-visible`为不使用指点设备(鼠标、触摸等)的人导航...).
这意味着我们可以依靠键盘导航锁定目标人群。

截至 2019 年 4 月，它只在 Chrome 中实现了旗帜后面的**和**。然而，好消息是，如果我们仍然想使用这个，我们仍然可以通过使用一个聚合填充 github.com/WICG/focus-visible。

如果在您的代码库中，您可以删除您已有的`outline : none`，安装这个 polyfill，并用这个
更新您的 CSS(作为第一步)

```
/*
  This will hide the focus indicator if the element receives focus via the mouse,
  but it will still show up on keyboard focus.
*/
.js-focus-visible :focus:not(.focus-visible) {
  outline: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

**这个选择器会过滤掉一个键盘事件所聚焦的所有元素，在那种情况下不会应用，所以你可以默认保持默认的`outline`。**

我非常喜欢这个规范中的一个小细节，即当用户用鼠标或键盘聚焦一个`<input>`字段时，`:focus-visible`也适用。为什么？规则很简单，如果一个交互元素打开了键盘(在手机上),它应该保持焦点环。我完全同意这一点，即使在桌面上有一个你的光标在哪里的指示，只能是一件好事。

一位同事向我展示了一个很好的例子，告诉我如何疯狂地使用键盘导航，甚至还有基于导航方向的动画:[sbb.ch/en/home.html](https://www.sbb.ch/en/home.html)

所以，让我们结束这个`:focus { outline: none; }`时代吧，现在我们有了一个可靠的解决方案，可以为每个人工作，也可以让很多人受益。

* * *

### *资源*

好好读读 github.com/WICG/focus-visible/issues/128`focus-visible`:
背后的意图

规格:
[drafts.csswg.org/selectors-4/#the-focus-visible-pseudo](https://drafts.csswg.org/selectors-4/#the-focus-visible-pseudo)