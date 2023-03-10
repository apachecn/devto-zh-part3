# 开始使用 CSS 中的可变字体

> 原文：<https://dev.to/techrocks/get-started-with-variable-fonts-in-css-10e8>

可变字体是一种技术，它可以将多种字体合并到一个文件中，而不是为每种宽度、粗细或风格创建一个单独的字体文件。字体允许你改变饱和度，比例，对比度，大小和动画。可变字体是在 2016 年 OpenType 版本中引入的。这项技术是在苹果、微软、Adobe 和谷歌公司的帮助下开发的。

如果您想了解字体变化是如何工作的，请在 Lawrence Pinna 网站上使用 OpenType 可变字体:http://www.axis-praxis.org/. Axis-Praxis 网站旨在展示可变字体在 web 浏览器中的功能。macOS、Windows 和 Linux 上的 Chrome v62+支持可变字体，macOS 10.13 High Sierra 和 iOS 11 上的 Safari 11 支持可变字体，Windows 10 v1709 的 Windows Insider 预览版中的 Microsoft Edge 支持可变字体。

注意，macOS Sierra 上的 Safari 不支持可变字体。你需要升级到下一个版本的操作系统，或者在 Chrome 上运行网站。对任意数量的轮廓使用一个文件的能力也允许节省空间。要将具有多种轮廓的字体(普通、浅色和粗体)连接到站点，您至少需要包括三个文件。

事实上，对于每种风格，您都需要进行连接。ttf 网络字体，。woff web 字体和一个用于 IE .eot 的 compact web 字体，对于每种样式，浏览器都会下载一个文件，这当然会影响内容显示的时间。

对于可变字体，只需要包含两个文件:。ttf 和 woff。浏览器本身将为它选择合适的文件并下载它。服务器请求的数量从 3 个减少到 1 个。因此，网站上的文字会出现得更快。

CSS4 支持字体粗细、字体拉伸、字体样式、字体光学大小属性的字体变化。

正如我上面提到的，Safari 中的 macOS Sierra 不支持这项技术，但是 macOS High Sierra 已经支持了。所以一切都很好，你可以开始处理它们了。