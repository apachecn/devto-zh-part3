# 见见 Creepyface，我的小副业

> 原文：<https://dev.to/4lejandrito/meet-creepyface-my-little-side-project-4f3m>

大家好，

我要介绍 [Creepyface](https://creepyface.io) ，我的副业。Creepyface 是[一个小的 JavaScript 库](https://github.com/4lejandrito/creepyface)，我做它是为了让我的脸跟随在线简历上的鼠标指针。我决定与大家分享它，这是:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[4le jandrito](https://github.com/4lejandrito)/[creepy face](https://github.com/4lejandrito/creepyface)

### 一个 Javascript 库，让你的脸跟着指针走。🤪🖱️👆

<article class="markdown-body entry-content container-lg" itemprop="text">

# [creepy face](https://creepyface.io)[![GitHub license](img/a5ee68a95d8f457ed498c54780ce4e61.png)](https://github.com/4lejandrito/creepyface/blob/master/LICENSE)[![npm version](img/9a180a8f78d7478ba8e5c1a1e70fa586.png)](https://www.npmjs.com/package/creepyface)[![Build](img/d4a71bce5308bb2de33cedec9cc12612.png)](https://github.com/4lejandrito/creepyface/actions?query=workflow%3ABuild+branch%3Amaster)[![Coverage Status](img/a3b8d66830876663bc31e7ce97dfa042.png)](https://coveralls.io/github/4lejandrito/creepyface?branch=master)[![Follow on Twitter](img/709e1ad62f0be5ef133c9bcff22bba27.png)](https://twitter.com/intent/follow?screen_name=creepyface_io)

Creepyface 是一个小 JavaScript 库，让你的脸看着指针(或者[跳舞](https://raw.githubusercontent.com/4lejandrito/creepyface/master/packages/creepyface-dance)！).

在[creepypface . io](https://creepyface.io)上观看它的运行，并使用向导[创建自己的一个。](https://creepyface.io/create)

如果你用 React，那就看看[<【creepy face/>](https://raw.githubusercontent.com/4lejandrito/creepyface/master/packages/react-creepyface)。如果你喜欢萤火虫，去看看[creepypface-firefly](https://raw.githubusercontent.com/4lejandrito/creepyface/master/packages/creepyface-firefly)。

[![Example animated gif of a face looking at the pointer](img/87a7829efc0b92bf7802df6a41b2fd40.png)](https://raw.githubusercontent.com/4lejandrito/creepyface/master/example.gif)

野外的 Creepyface:

*   [https://atroshenkonikita.com](https://atroshenkonikita.com)
*   [https://www.yitzi.dev](https://www.yitzi.dev)
*   [https://1aville.ch/about.html](https://1aville.ch/about.html)
*   [https://github.com/reflog/mattermost-plugin-creepy](https://github.com/reflog/mattermost-plugin-creepy)

## 使用

```
<script src="https://creepyface.io/creepyface.js"></script>
<img
  data-creepyface
  src="https://creepyface.io/img/0/serious"
  data-src-hover="https://creepyface.io/img/0/hover"
  data-src-look-0="https://creepyface.io/img/0/0"
  data-src-look-45="https://creepyface.io/img/0/45"
  data-src-look-90="https://creepyface.io/img/0/90"
  data-src-look-135="https://creepyface.io/img/0/135"
  data-src-look-180="https://creepyface.io/img/0/180"
  data-src-look-225="https://creepyface.io/img/0/225"
  data-src-look-270="https://creepyface.io/img/0/270"
  data-src-look-315="https://creepyface.io/img/0/315"
/>
```

> 在 [codepen](https://codepen.io/4lejandrito/pen/vbgxEB) 上运行这个例子。

Creepyface 将自动检测您的图像(多亏了`data-creepyface`属性),并根据您使用的设备使其注视鼠标或手指。

你可以加上…

</article>

[View on GitHub](https://github.com/4lejandrito/creepyface)

我试图将 Creepyface 设计得尽可能简单易用。这就是让你的脸变得有活力所需要的一切:

```
<script src="https://creepyface.io/creepyface.js"></script>

<img 
  data-creepyface
  src="https://creepyface.io/img/0"
  data-src-hover="https://creepyface.io/img/0/hover"
  data-src-look-0="https://creepyface.io/img/0/0"
  data-src-look-45="https://creepyface.io/img/0/45"
  data-src-look-90="https://creepyface.io/img/0/90"
  data-src-look-135="https://creepyface.io/img/0/135"
  data-src-look-180="https://creepyface.io/img/0/180"
  data-src-look-225="https://creepyface.io/img/0/225"
  data-src-look-270="https://creepyface.io/img/0/270"
  data-src-look-315="https://creepyface.io/img/0/315" 
/> 
```

您可以使用[creepypface 的向导](https://creepyface.io/create)在各个方向拍摄自己的照片。

欲了解更多信息，请访问 Creepyface 的 GitHub。

如果你觉得 Creepyface 有用，请告诉我。欢迎任何反馈。

谢谢！