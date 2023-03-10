# 草图单元插件 2.0.0-rc.1 可用

> 原文：<https://dev.to/dploeger/sketch-units-plugin-200-rc1-available-335>

以防你不知道，[草图](https://www.sketchapp.com/)是一个很棒的~~矢量~~，~~像素~~，嗯...macOS 的“vecxel”设计应用程序。你可以用它来设计模型、图标、屏幕等等。

Sketch 可以使用 Javascript 编写的插件进行扩展(通过[摩卡](https://github.com/logancollins/Mocha)和 [CocoaScript](https://github.com/ccgus/CocoaScript) 实现)。

几天前，我写了一个转换像素的插件，通过使用一个 DPI 值和一个因子，草图将像素作为一个基本单位转换成各种现实生活中的单位。因此，当您将 Sketch 中的某些内容导出为 PDF 时，它将以 72 的 DPI 值导出。基于此，你可以非常简单地将像素单位转换成你想要的任何单位。

然而，该插件的 1.0 版本设计得并不太好，当时可用的开发工具也非常基础。

此外，1.0 版本对对话框 ui 使用了 CocoaScript，这是一个真正的痛苦。

如今， [skpm](https://skpm.io/) 是可用的，这是一个为草图单元构建开发环境的好工具。此外，有人提出了一个库，使用 WKWebview 在浏览器窗口中[渲染草图用户界面。这样，我可以简单地包装一个引导 UI 和一些 JQuery 魔法来构建我的对话框。](https://github.com/skpm/sketch-module-web-view)

所以，宣布插件的版本 [2.0.0-rc.1](https://github.com/dploeger/sketch-plugin-units/releases/tag/v2.0.0-rc.1) 。如果你正在使用 Sketch 或者你对如何编写 Sketch 插件感兴趣，你可能[想看看这个](https://github.com/dploeger/sketch-plugin-units/)。

*[克里斯平·塞门斯](https://flic.kr/p/TS1LH)的封面照片*