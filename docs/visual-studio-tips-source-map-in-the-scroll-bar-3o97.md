# Visual Studio 提示-滚动条中的源地图

> 原文：<https://dev.to/hutchcodes/visual-studio-tips-source-map-in-the-scroll-bar-3o97>

您是否发现自己在 Visual Studio 的一个大文件中上下滚动？“在滚动条中显示源地图”给你一个 1000 英尺的代码视图，并允许你通过简单的点击来导航。

要打开此功能，请转到工具->选项->文本编辑器->滚动条。在“行为”部分，选择“对垂直滚动条使用贴图模式”。我也喜欢选择“显示预览工具提示”。你可以选择你自己的宽度，但我已经很满意“中等”了。

[![alt text](img/bb5c5f0de755ecd46f14302e53229e25.png "Visual of turning on Source Map scroll bar.")](https://res.cloudinary.com/practicaldev/image/fetch/s--W4GQL3Rl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hutchcodes.net/img/2019/TurnOnSourceMap.jpg)

一旦完成，你就可以像这样导航你的代码

[![alt text](img/05f4159e6bb6180508b9052bd52bc936.png "Visual of turning on using the Source Map scroll bar.")](https://res.cloudinary.com/practicaldev/image/fetch/s--lZ5zE7yS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://hutchcodes.net/img/2019/SourceMapScrollBar.gif)

注意，在源图上有 3 个工件。

1.  指示您在文档中所处位置的白色条
2.  源地图左侧的白色块表示书签
3.  源映射左侧的红色块表示断点