# Direct3D 中的 FreeSync 2 支持编程

> 原文：<https://dev.to/reg__/programming-freesync-2-support-in-direct3d-51oa>

AMD 刚刚展示了 [Oasis 演示](https://community.amd.com/community/gaming/blog/2019/02/27/introducing-the-amd-radeon-freesync-2-hdr-technology-oasis-demo)，展示了其 FreeSync 2 HDR 技术的使用。如果您想知道如何在您的 Windows DirectX 程序或游戏中实现相同的功能(不管您使用 D3D11 还是 D3D12)，这里有一篇文章适合您。

但是首先，声明:虽然我已经把它放在了我的“关于”页面上，我想强调这是我的个人博客，所以这里的所有观点都是我自己的，并不代表我的雇主。

镭龙自由同步(其新的官方网页在这里:[镭龙自由同步技术|自由同步 2 HDR 游戏](https://www.amd.com/en/technologies/free-sync))是一种 AMD 技术，涵盖了两种不同的东西，这可能会引起一些混淆。首先是可变刷新率，其次是 HDR。两者都需要有显示器支持。FreeSync 兼容显示器及其参数的数据库为: [Freesync 显示器](https://www.amd.com/en/products/freesync-monitors)。

# 1。可变刷新率

传统上，呈现新渲染的帧有两种模式。垂直同步(V-sync)关闭时，它会立即发生。游戏然后尽可能快地渲染新的帧，但在扫描线过程中可能会翻转到新的帧，这会导致令人不愉快的效果，称为“撕裂”。另一方面，当垂直同步开启时，图形卡必须等待显示新帧，直到发生新的显示器刷新(垂直同步)，刷新速度与显示器刷新率(通常为 60 Hz)保持一致。以每秒帧数(FPS)衡量的游戏性能则受限于该频率。(请注意 FPS 是相同的单位——以 Hz 为单位的频率= 1/秒)。只要 GPU 能够按时渲染帧，游戏就可以流畅地运行，没有撕裂。如果没有，它需要等到下一个垂直同步，这会导致明显的断续。

可变刷新率以 VESA Adaptive-Sync(在 FreeSync 中实现)的形式添加到 DisplayPort 和 HDMI 协议中，允许 GPU 在新帧就绪时通知显示器。屏幕刷新可以在不规则的时间间隔内发生，这结合了上述两种方法的优点-在支持的刷新率范围内没有撕裂和不连贯(就我的显示器而言:LG 32GK850F，它是 48-144 Hz)。

为了启用它:

1.  有一个 FreeSync 兼容的显示器。
2.  通过 DisplayPort 或 HDMI 线连接到 AMD 显卡。
3.  如果适用，在监视器菜单中启用 FreeSync。
4.  在驱动设置中启用 free sync-进入开始菜单→ AMD 镭龙设置→显示选项卡→镭龙 FreeSync = On。
5.  你不需要在你的应用程序中做任何特别的事情来让它工作！只要确保它呈现得像你打开了 V-sync，这意味着用`SyncInterval` = 1 而不是 0 来调用`IDXGISwapChain::Present`。否则我的显示器报告可变刷新率仍然工作，但撕裂是可见的，所以没有太大意义。

事情是这样的:

*   如果您的程序能够以您在设置全屏参数(无论是 60、100 还是 144)时选择的刷新率或更高的刷新率来提供帧，那么 FPS 会被限制在该频率，程序会平稳运行，就像它打开了 V-sync 一样。
*   如果你的程序工作在较低的帧速率，但仍然高于显示器的最低帧速率(比如我的情况是 48)，那么这就是你得到的帧速率——显示器根据它进行调整，当它准备好时显示每一帧，一切仍然工作，看起来很好，没有太多的口吃。
*   当你的帧率低于这个值时，当然动画会变得不太流畅，但仍然有一些机制在努力确保最佳的体验，称为[低帧率补偿(LFC)](https://www.amd.com/Documents/freesync-lfc.pdf) 。

我尝试了许多不同的显示设置。他们都没有禁用可变刷新率。

*   使用功能`IDXGISwapChain::​SetFullscreenState(TRUE, NULL)`进入了独占全屏，但也尽量不做，只是创建一个覆盖整个屏幕的无边框窗口。
*   使用 AMD AGS 库，用`mode = AGSDisplaySettings::​Mode_Freesync2_scRGB`调用`agsSetDisplayMode`，但也跳过了这样做。
*   试过`DXGI_MODE_DESC::​ScanlineOrdering = DXGI_​MODE_​SCANLINE_​ORDER_​UNSPECIFIED`或`DXGI_MODE_SCANLINE_ORDER_PROGRESSIVE`。
*   试过`DXGI_SWAPCHAIN_DESC::​SwapEffect = DXGI_​SWAP_​EFFECT_​FLIP_​DISCARD`、`DXGI_​SWAP_​EFFECT_​FLIP_​SEQUENTIAL`、`DXGI_​SWAP_​EFFECT_​DISCARD`、`DXGI_​SWAP_​EFFECT_​SEQUENTIAL`。

# 2。直接热轧制

另一件事是显示高动态范围(HDR)亮度的颜色。市场上有各种支持 HDR 的显示器和电视，它们可以通过各种 GPU 进行控制。在我之前的博客文章中，我研究了关于[编程 HDR 显示器支持](http://asawicki.info/news_1703_programming_hdr_monitor_support_in_direct3d.html)的话题，我在 AMD、Nvidia 和 Intel 上做了实验。

现在，随着我对它的了解越来越多，我想描述一下推荐的激活 FreeSync 2 支持的 HDR 的步骤。通过这项技术，您可以查询显示器的性能(最大亮度单位为尼特，XY 原色为红/绿/蓝等。)并在游戏的后处理过程中相应地调整色调映射。这样你就有更多的信息和对过程和最终结果的完全控制！为此:

*   按照上述步骤 1-4-使用兼容的显示器，启用自由同步。
*   在你的程序中，使用 [AMD AGS](https://github.com/GPUOpen-LibrariesAndSDKs/AGS_SDK/) 库:
    *   `#include <ags_lib/inc/amd_ags.h>`
    *   用“amd_ags_x64.lib”链接。
    *   将文件“amd_ags_x64.dll”与程序捆绑在一起。
    *   调用函数`agsInit`初始化库。
*   检查由`agsInit`填充的结构`AGSGPUInfo`。找到 GPU，然后显示你的兴趣。记住它描述的`AGSDisplayInfo`结构。姑且称之为`dispInfo`。
*   通过检查`(dispInfo.displayFlags & AGS_DISPLAYFLAG_FREESYNC_2) != 0`是否支持 FreeSync 2。
*   在`DXGI_​FORMAT_​R16G16B16A16_​FLOAT`中创建 D3D 交换链。
*   通过调用`IDXGISwapChain::​SetFullscreenState(​TRUE, NULL)`进入独占全屏模式。
*   用以下参数填充`AGSDisplaySettings`结构，并使用`agsSetDisplayMode`函数提交。

```
AGSDisplaySettings settings;
settings.mode = AGSDisplaySettings::Mode_Freesync2_scRGB;
settings.chromaticityRedX = dispInfo.chromaticityRedX; // Just copy them all.
settings.chromaticityRedY = dispInfo.chromaticityRedY;
settings.chromaticityGreenX = dispInfo.chromaticityGreenX;
settings.chromaticityGreenY = dispInfo.chromaticityGreenY;
settings.chromaticityBlueX = dispInfo.chromaticityBlueX;
settings.chromaticityBlueY = dispInfo.chromaticityBlueY;
settings.chromaticityWhitePointX = dispInfo.chromaticityWhitePointX;
settings.chromaticityWhitePointY = dispInfo.chromaticityWhitePointY;
settings.minLuminance = dispInfo.minLuminance;
settings.maxLuminance = dispInfo.maxLuminance;
settings.maxContentLightLevel = dispInfo.maxLuminance; // !!
settings.maxFrameAverageLightLevel = dispInfo.maxLuminance * 0.5; // !!
settings.flags = 0;
agsSetDisplayMode(agsContext, deviceIndex, displayIndex, &settings); 
```

*   将颜色值写入线性空间的交换链中，不进行 gamma 校正。缩放它们并调整色调映射，使最大亮度为 maxLuminance/80。这是因为 scRGB 标准将值 1 定义为 80 尼特。在我的显示器中，最大亮度= 496，我需要输出 0 到 496/80 = 6.2 之间的值。

通过对填充结构使用这些特定值，您可以确保颜色按原样显示，而无需在监视器中进行额外的色调映射或其他后处理，这可以提高质量并减少延迟。在实践中，我看不出与仅仅以简单的方式启用 HDR 有什么不同，正如我在[我的上一篇文章](http://asawicki.info/news_1703_programming_hdr_monitor_support_in_direct3d.html)中所描述的，但这只是理论。

就像在以前的帖子中一样，我在这里只关注高动态范围的亮度，而不是色域。这是因为今天大多数内容管道仍然使用传统的 SDR sRGB 颜色空间用于纹理和所有输入图形。在宽色域中创作它们或将图像重新映射到显示器报告的色域是另一个复杂的主题，它在未来将变得更加重要。

最后，让我再重复一遍:我在这里描述的一切只是我个人的知识和对我有用的轶事证据，而不是官方指南。但是，由于我找不到任何其他公开可用的关于这项技术的信息，我想这比什么都没有要好:)

我测试使用的平台:OS = Windows 10 64 位版本 1809 (OD Build 17763.316)，GPU =镭龙 RX Vega 56，driver = 19.2.3，monitor = LG 32GK850F，cable = DisplayPort。