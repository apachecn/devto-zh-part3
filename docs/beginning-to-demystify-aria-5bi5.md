# 开始揭开咏叹调的神秘面纱

> 原文：<https://dev.to/lkopacz/beginning-to-demystify-aria-5bi5>

**原贴于[a11ywithlindsey.com](https://www.a11ywithlindsey.com/blog/beginning-demystify-aria)T3】**

[https://api.parler.io/ss/player?url=https://www.parler.io/audio/7119149108/67927009fe10c9ec137aa9ff973489b3948157c0.a5c18b68-29ba-482a-8c62-3e2c033c4447.mp3](https://api.parler.io/ss/player?url=https://www.parler.io/audio/7119149108/67927009fe10c9ec137aa9ff973489b3948157c0.a5c18b68-29ba-482a-8c62-3e2c033c4447.mp3)

如果你还不知道的话，我通常会把这些都交叉贴在 [DEV](https://dev.to) 上。当我交叉发布为什么可访问性不是一个边缘案例时，我得到了一个希望可访问性更容易实现的评论。一旦我了解了最新的 HTML 和 CSS 最佳实践，我并不觉得学习制作可访问的解决方案有什么挑战性，所以我真的很好奇他们在哪里发现了困难。当[他们回答](https://dev.to/marek/comment/882m)时，似乎主要的痛点是咏叹调。

我已经看到了很多关于 ARIA 以及什么时候该用什么时候不该用的困惑。这些 ARIA 属性到底是什么意思？是否有所有属性的列表？我正在考虑把它做成一个系列，也许一周检查几个 ARIA 属性，以及如何使用它们，我是否喜欢它们，等等。在我深入讨论 ARIA 的所有属性之前，我真的需要弄清楚 ARIA 的用途，以及我认为你不应该用它来做什么。

## 咏叹调是什么鬼？

ARIA 是一组属性，您可以将它们添加到元素中，赋予它们额外的上下文和含义。这对很多事情都很有用。以下是我觉得对艾瑞亚有帮助的地方:

*   告诉您何时加载动态内容
*   当重要的事情出现时提醒用户
*   当状态改变时告诉屏幕阅读器
*   如果需要，添加一些额外的上下文

关于艾瑞亚的文件相当吓人。即使作为一个容易接近的人，我也觉得这有点令人生畏。我了解 ARIA 的方式是通过打开一个像 Twitter 这样的常用应用程序。然后，我检查了元素，在 HTML 中搜索了`aria`，查看了所有的属性。然后我打开我的屏幕阅读器，实际上听了当我到达那个特定元素时实际上宣布了什么。比如我在 Mac 上使用 VoiceOver，打开 Twitter 的主页，这是我的个人资料图片在右上角的标记:

```
<a href="/settings" id="user-dropdown-toggle" role="button" aria-haspopup="true">
<!-- Children Elements --> 
</a> 
```

**注意:为了可读性，我已经从标记**中删除了一些属性和类

我一眼就看出了两个 ARIA 属性:`role="button"`和`aria-haspopup="true"`。当我使用屏幕阅读器时，这意味着什么。

[![VoiceOver text that says Profile and Settings, pop up button](img/030ced9e999f80786f2fec9c0b711e51.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---5dm8hMf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/profile-settings-popup-button-voiceover-36fb08e8f2a20bc5e114a7de4c3d0fab-a408b.png) 

<figure>

<figcaption>第一件事画外音说。</figcaption>

</figure>

[![VoiceOver text that says You are currently on a po pup button, to display a list of options press control option space.](img/a86d75b96811e755f7f3eb4cf6c8cb99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ocHD4Fi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/popup-button-instructions-voiceover-22c146abab05f0f1e18e2f085892b2cb-a408b.png) 

<figure>

<figcaption>第二件事画外音说。</figcaption>

</figure>

如果你看到阅读的内容，你会注意到“弹出按钮”这几个字因为标记是一个链接，如果没有这些属性，它看起来只是一个链接。老实说，我认为这个标记可以改为`<button>`,因为在阅读本文时，我不能使用链接转到`href`属性值(/settings)并像按钮一样工作。但是知道这是一个弹出窗口的**上下文**可能对屏幕阅读器非常有帮助。

我会说，当我第一次开始构建东西时，我通常会犹豫是否使用 ARIA。您可能已经注意到，我对我将要更改的标记进行了评论，正因为如此，我尽量避免使用 ARIA，除非有必要添加上下文。这就是为什么在我的技术文章中，当我从头开始写东西的时候，你可能一开始看不到 ARIA 属性。我有相当多的原因导致我...

## 什么唱段不是？

我第一次在聚会上介绍 ARIA 的时候，一个盲人开发者也在场，他质问我。一点都不好玩。这样做的原因是因为很多时候 ARIA 不应该成为可访问性的焦点，尤其是现在我们有 HTML5。HTML5 解决了很多屏幕阅读器曾经面临的语义问题。

艾瑞亚不是这样的:

*   语义 HTML 的替代品

嗯，我能想到的就这些了。让我们把这个分解成一个例子，以便更好地理解我的意思。如果你不喜欢`<button>`的默认样式，但是你喜欢所有的内置按钮特性，这个 HTML 不会神奇地让所有的按钮默认都工作:

```
<div role="button">
  Open Menu
</div> 
```

假设您有一个按钮的点击事件。如果你读过我写的关于提高键盘易用性的 3 个简单技巧的文章，你就会知道按钮有一些内置的默认功能。不做任何其他事情，所有这一切都是宣布这是一个带有屏幕阅读器的按钮。然而，如果没有键盘，你就无法专注于它。所以下一步是确保我们有一个`tabindex`在上面。让我们试试那个。

```
<div tabindex="0" role="button">
  Open Menu
</div> 
```

我们现在能在它上面使用一个点击事件吗？让我们试一试。让我们更新 HTML 以包含一个 ID，使它更容易用 JavaScript:

```
<div tabindex="0" id="button" role="button">
  Open Menu
</div> 
```

现在，我将在这里写一些基本的 JavaScript，看看它是否有效。

```
const button = document.getElementById('button');
button.addEventListener('click', () => alert('clicked!')); 
```

[![Gif of Open Menu div being clicked with an alert, then being tabbed and receiving focus that doesn't work.](img/f3cd31d79a31184198146cc5dbac6826.png)](https://i.giphy.com/media/fGCfnD73EyICoydoCS/giphy.gif)

从 gif 上可以看到，ARIA 角色或者 tabindex 没有修复。我们必须监听按钮上的按键事件。我将和您一起调试这个程序，这样我们就可以了解这需要做多少额外的工作。

```
button.addEventListener('keypress', (e) => {
  console.log(e)
}) 
```

控制台日志结果:
[![Console log of the event object, particularly showing the code is Enter](img/78a2921b80cb272a7a441a5b4e22eb3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EjWuBSNq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/console-logging-keypress-event-2a2a4b9159b5567c48370dbdd7373668-a408b.png)

让我们想想我们在这里做什么。我在按钮上添加了一个按键事件，并检查了事件对象中的属性。这很重要，因为如果我们想要复制一个按钮，我们必须注意当我们有按键事件时，我们按下了什么键。内置的 HTML 按钮行为对按键、回车键和空格键都有效。当我们查看控制台日志时，我们希望使用`e.code`。这是我用条件更新的内容。

```
button.addEventListener('keypress', (e) => {
  if (e.code === 'Enter' || e.code === 'Space') {
    alert('pressed!')
  }
}) 
```

[![Gif of Open Menu div going into focus and getting an alert saying pressed!](img/41e3331ceb7118bb4962b222335431bc.png)](https://i.giphy.com/media/1qj5fXKoJmeeoy9309/giphy.gif)

因此，虽然这不是最具挑战性的事情，但是将`<div>`改为`<button>`要容易得多。您可以使用 CSS 来摆脱默认的样式，总的来说，您将使您的生活更容易访问！

## 结论

我喜欢用语境来思考咏叹调。有时候你不需要额外的背景 HTML 已经足够了。有时它可能会更好，尤其是如果它是一个定制的小部件。关于 ARIA 的具体属性和它们的作用，我将在以后做更多的介绍——主要是因为文档可能有点令人生畏。为此干杯！

如果你对这篇博文有任何疑问，请随时在 Twitter 上联系我！