# 禁用谷歌浏览器开发工具在检查模式下的详细工具提示

> 原文：<https://dev.to/orliesaurus/disable-detailed-tooltips-in-inspect-mode-for-google-chrome-dev-tools-4390>

我一生中有 3/4 的时间花在谷歌 Chrome DevTools 上。在 Google Chrome Dev tools 的最新版本中，有人想出了一个好主意，在鼠标悬停时检查 HTML 元素时弹出一个侵入性的工具提示。
本:
[![This](img/111d579bfb5817da9751637ab82e97c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a2KISMKp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.snag.gy/6B5irK.jpg)

谷歌这样描述这一变化:

> 当检查一个节点时，DevTools 现在会显示一个扩展的工具提示，包含一些常见的重要信息，如字体大小、字体颜色、对比度和盒子模型尺寸。

我真的不认为字体大小是重要的信息，事实上我觉得这“太多了”。这些简单的 CSS 属性很可能不是我在像素完美、轻量级、反应式网页的开发之旅中所担心的(讽刺)。事实上，当我剖析节点树中的 html 元素时，这可能是我需要的最不重要的信息。

> 新的 Chrome 元素检查器完全是垃圾。谁他妈的把那当成功能请求了 [@ChromeDevTools](https://twitter.com/ChromeDevTools?ref_src=twsrc%5Etfw) ？
> 
> — orlie ([@orliesaurus](https://dev.to/orliesaurus) ) [March 27, 2019](https://twitter.com/orliesaurus/status/1110952009396600832?ref_src=twsrc%5Etfw)

## 如何防止这种详细的工具提示出现？

没有办法永久禁止弹出窗口的显示，但是我发现在网页上移动鼠标的同时按住键盘上的 CTRL(或 CMD 或等效的键)可以禁止弹出窗口的显示。

## 没坏为什么要修？

所有这些信息都在下面的开发工具面板中快速重复。为什么需要弹出工具提示？如果您对确定某个元素的属性感兴趣，只需查看下面的窗格。

这是禁用工具提示的最好方法吗？大概不会。不过，目前只能这样了。我确实觉得开发工具的这个附加功能很烦人，但我也是一名开发人员，我喜欢抱怨。