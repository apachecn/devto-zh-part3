# 建立一个图像优化工具，以赢得自动驾驶页面速度游戏

> 原文：<https://dev.to/rsedykh/building-an-image-optimization-tool-to-win-the-pagespeed-game-on-autopilot-1cfj>

你好戴夫.托。

我们正在构建一个名为 [PageDetox](https://pagedetox.com?utm_source=dev.to) 的图像优化工具。它的目的是帮助赢得谷歌页面速度游戏。

**我想问你的网址进行分析，并为你提供优化指导，以提高你的形象得分高达 a💯。**

这个工具是如何工作的？

*   你在自动驾驶仪上分析一个页面，请求一份报告
*   我们提供该报告以及用于优化图像的个性化工作流程

分析器已经准备就绪，而逐步优化部分正在进行中。

这是我们想要开始的指令设计:

*   上传文件到图像 CDN *(因为我们和 [Uploadcare](https://uploadcare.com?utm_source=dev.to) 合作，我们坚持使用这项服务；如果你喜欢其他解决方案，请选择你最喜欢的。Uploadcare 提供了一个 CDN URL，允许即时应用图像转换，我们利用了它)*
*   获取那些 CDN URLs
*   用`srcset`替换`<img>`中的`src`属性
*   将分析结果考虑在内，使用即时转换制作源图像的每个版本

下面查一个⬇️的例子