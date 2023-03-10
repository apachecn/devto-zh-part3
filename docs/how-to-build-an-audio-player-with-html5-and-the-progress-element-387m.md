# 如何用 HTML5 和 Progress 元素构建一个音频播放器

> 原文：<https://dev.to/lukewduncan/how-to-build-an-audio-player-with-html5-and-the-progress-element-387m>

HTML5 通过音频标签引入了内置的媒体支持，这使得用有限的代码将媒体直接嵌入任何 HTML 文档变得非常容易。它带有内置的浏览器控件，如果你指定和播放音频很好，很有效。

这通常是它在您的浏览器中的外观。

[![Standard Browser HTML5 Audio Player](img/17fc82e41ffd267f0b3db78dbaa4cd2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s6GgSlyv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AmxPFTYyWn0iq3e2yHzazPg.png)

你可能会想:“如果我想用自己的按钮和滚动条来设计自己的音频播放器呢？”

有了音频元素、进度元素和几个按钮，你可以立刻构建你自己的音频播放器！progress 元素主要用于跟踪任务的完成情况，可以使用 Javascript 轻松操作。在我们的例子中，它将记录一首歌的长度。最棒的是，HTML5 和浏览器功能使获取歌曲长度、当前时间等变得容易。

如果你想玩代码，去吧！不幸的是，Codepen 嵌入对我不起作用，所以链接在下面。

点击此处查看 Codepen！

下面是代码。请注意，它们是两个独立的函数。一个跟踪实际播放音频的音频播放器(initPlayers 函数)，另一个跟踪 progress 元素的音频(initProgressBar 函数)。我试图将 jQuery 保持在最低限度，但实际上用它来调用函数，对不起，我不对不起 JS 纯粹主义者。