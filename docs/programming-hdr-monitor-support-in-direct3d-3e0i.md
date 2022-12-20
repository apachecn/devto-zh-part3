# 在 Direct3D 中编程 HDR 监视器支持

> 原文：<https://dev.to/reg__/programming-hdr-monitor-support-in-direct3d-3e0i>

我得到了一个支持 HDR 的显示器(LG 32GK850F)，所以我开始学习如何通过编程来使用它的功能。我还有很多要学，因为有很多关于色彩空间的理论要消化。，但在这篇博文中，我想直奔主题:如何在你的 C++ DirectX 程序中启用 HDR？为了测试这一点，我使用了来自 3 个不同的 PC GPU 供应商的 3 个图形芯片。下面你可以看到我的实验结果。

您需要知道有两种不同的方法来查询显示能力，以及控制其工作参数。

(1)首先是标准的 Windows，独立于 GPU 的 API 叫做**[【DXGI】](https://docs.microsoft.com/pl-pl/windows/desktop/direct3ddxgi/dx-graphics-dxgi)**。它以 COM API 的形式与 Windows SDK 一起提供。这是 Direct3D 11 和 12 的通用层(你甚至可以[和 Vulkan](http://asawicki.info/news_1695_there_is_a_way_to_query_gpu_memory_usage_in_vulkan_-_use_dxgi.html) 一起使用它)。与 HDR 相关的功能被添加到 API 的最新更新中，以新接口的形式出现，在末尾有一个数字。要访问它们，您必须首先查询它们的基本对象。示例:

```
IDXGIOutput* output = /* initialize output */;
IDXGIOutput6* output6;
HRESULT hr = output->QueryInterface(__uuidof(IDXGIOutput6), (void**)&output6);
if(SUCCEEDED(hr)) {
   // Use output6...
   output6->Release();
} else {
   // Error!
} 
```

只有安装了足够新版本的 Windows SDK，您才能成功编译此代码。只有当用户拥有足够新版本的 Windows 10 时，代码才会成功执行(而不是因错误代码而失败)。

然后，您可以通过调用函数`IDXGIOutput6::GetDesc1`来查询监视器功能。填充结构`DXGI_OUTPUT_DESC1`,它描述了可用的色彩空间、每个组件的位数、红/绿/蓝原色、白点以及设备上可用的亮度范围。

为了通知系统预期的 HDR 用法，你可以调用填充了结构`DXGI_HDR_METADATA_HDR10`的函数`IDXGISwapChain4::SetHDRMetaData`，在这里你传递你的参数——同样是红/绿/蓝、白点和亮度范围。还有`IDXGISwapChain3::SetColorSpace1`功能，让你通过枚举`DXGI_COLOR_SPACE_TYPE`，告诉你使用什么颜色空间(RGB 或 YCbCr，线性或伽玛 2.2，P709 或 P2020 等)。)

(2)有不同 GPU 厂商的自定义 API。

2.1.在 AMD 的情况下，我们正在谈论称为 **[AMD AGS](https://github.com/GPUOpen-LibrariesAndSDKs/AGS_SDK/)** 的库。要在 C++代码中使用它，您需要:

*   #包括
*   用“amd_ags_x64.lib”链接。
*   将文件“amd_ags_x64.dll”与您的游戏捆绑。
*   调用函数`agsInit`初始化库。

要查询监视器功能，请检查由`agsInit`填充的结构`AGSGPUInfo`。包含的结构`AGSDisplayInfo`提供关于连接的显示器的信息，包括`displayFlags`(告诉支持什么 HDR 模式)、原色、白点和最小/最大/平均亮度。

要启用 HDR 模式，调用`agsSetDisplayMode`功能。在`AGSDisplaySettings`结构中指定请求的参数。

2.2.以 Nvidia 为例，有一个库叫做 **[NVAPI](https://developer.nvidia.com/nvapi)** (下载需要免费注册)。要在 C++代码中使用它，您需要:

*   #包括<nvapi.h></nvapi.h>
*   与“nvapi64.dll”链接。
*   不需要捆绑一个 DLL 文件到你的游戏中。该库使用系统目录中的“nvapi64.dll ”,随图形驱动程序一起安装。
*   调用函数`NvAPI_Initialize`初始化库。

要查询监视器功能，首先枚举可用的 DisplayIds。然后调用`NvAPI_Disp_GetHdrCapabilities`。返回的结构`NV_HDR_CAPABILITIES`提供了关于显示器的 HDR 参数的信息——原色、白点、最小/最大亮度等。

要启用 HDR 模式，调用`NvAPI_Disp_HdrColorControl`功能。使用结构`NV_HDR_COLOR_DATA`指定请求的参数。

理论上是这样。下面是我在 3 个不同的 GPU 上的实验结果。所有都使用相同的 HDR 显示器(LG 32GK850F)，通过 HDMI 或 DisplayPort 电缆连接作为唯一的活动显示器，并安装了显示器驱动程序。我在每种情况下都使用了 Windows 10 64 位版本 1809 (OS Build 17763.316)。

# 1。GPU = AMD 镭龙 RX Vega 56，驱动= 19.2.3

`IDXGIOutput6::GetDesc1`返回的`DXGI_OUTPUT_DESC1`成员:

> BitsPerColor = 10
> 
> COLOR SPACE = DXGI _ COLOR _ SPACE _ RGB _ FULL _ g 2084 _ NONE _ p 2020
> 
> red primary = 0.669921875，0.3095703125
> 
> green primary = 0.2548828125，0.6796875
> 
> blue primary = 0.150390625，0.07515

`AGSDisplayInfo`拥有成员:

> DISPLAY flags = AGS _ 显示标志 _ 主要 _ 显示| AGS _ 显示标志 _ HDR 10 | AGS _ 显示标志 _ 自由同步| AGS _ 显示标志 _ 自由同步 _2
> 
> 色度红色= 0.6699，0.3096
> 
> 色度绿色= 0.2549，0.6797
> 
> 色度蓝色= 0.1504，0.0752
> 
> 色度白色点= 0.3135，0.3299

如你所见，除了`minLuminance`，DXGI 和 AGS 的参数非常匹配。

现在是启用 HDR 的测试:事实证明，要让它工作，唯一需要做的就是创建半浮点格式的交换链`R16G16B16A16_FLOAT`，而不是像`R8G8B8A8_UNORM`那样每个组件 8 位的传统交换链。

则值为 0..1 映射到 SDR，而高于 SDR 的值使事物更亮。获得最大 HDR 亮度的值似乎在 6.2 左右。这是有意义的，因为我们在这里使用 scRGB 标准(旨在向后兼容 SDR)，该标准将值 1 定义为 80 尼特，因此值 6.2 给出 496 尼特——正好是我的显示器报告的最大亮度。

同样重要的是要注意，你应该输出线性空间的颜色值，而不是伽马校正！

我尝试了许多可能与显示模式有关的不同事情，但似乎它们既不需要也不改变 HDR 行为中的任何东西:

*   窗口可以是无边框的，覆盖整个屏幕，但也可以是常规窗口。
*   我可以通过设置适当的分辨率、刷新率、扫描线顺序、缩放比例等进入“真”的全屏模式。还有打电话`IDXGISwapChain::SetFullscreenState(TRUE, NULL)`，但是我不用。
*   我试着用不同的参数调用`IDXGISwapChain4::SetHDRMetaData`——用绿基色替换红基色，用数量级改变亮度……没有观察到任何效果。
*   我试着打电话给`IDXGISwapChain3::SetColorSpace1`。值为`DXGI_COLOR_SPACE_RGB_FULL_G2084_NONE_P2020`的调用失败，尽管这是监视器报告的值。使用值`DXGI_COLOR_SPACE_RGB_FULL_G10_NONE_P709`成功了，但是没有改变什么，因为这就是我们在这里使用的颜色空间(RGB 颜色空间，G10 =指数为 1.0 的伽玛=线性空间，P709 =类似于 sRGB 的原色)。我试过用 gamma 2.2，但是用`DXGI_COLOR_SPACE_RGB_FULL_G22_NONE_P709`打电话也失败了。
*   我也试过打`agsSetDisplayMode`。同样，改变原色或亮度没有效果，将模式设置为`Mode_HDR10_scRGB`、`Mode_FreeSync2_scRGB`或`Mode_FreeSync2_Gamma22`也没有效果。唯一不同的是使用了`Mode_SDR`。然后我的程序以一种奇怪的方式运行，它只使用 SDR，但是它仍然期望范围值为 0..6.2，在线性空间中。

# 2。GPU = Nvidia GeForce GTX 1070，驱动= 419.17

`IDXGIOutput6::GetDesc1`返回的`DXGI_OUTPUT_DESC1`成员:

> BitsPerColor = 8
> 
> ColorSpace = DXGI _ COLOR _ SPACE _ RGB _ FULL _ g 2084 _ NONE _ p 2020
> 
> red primary、GreenPrimary、BluePrimary、WhitePoint 与 AMD
> 
> MinLuminance = 0 尼特
> 
> MaxLuminance = 1499 尼特
> 
> maxfullframegluminance = 799 尼特

哇，那太不一样了！是因为我使用不同的电缆和连接器类型，还是因为不同的显卡？我不知道。

NVAPI 函数`NvAPI_Disp_GetHdrCapabilities`返回以下`NV_HDR_CAPABILITIES`结构成员:

> isst 2084 eotfsupported = true
> 
> israditionalhdrgammasupported = false
> 
> isedr supported = true
> 
> driverExpandDefaultHdrParameters = false
> 
> israditionalsdrgammasupported = true
> 
> isDolbyVisionSupported = false
> 
> display _ data . display primary _ x0 = 33496，表示红色 X = 0.66992
> 
> 也就是说蓝色 X = 0.15038
> 
> display _ data . display primary _ y2 = 3759，也就是说蓝色 Y = 0.07518
> 
> display _ data . display white point _ X = 15673，也就是说 0.31346
> 
> display _ data . display white point _ Y = 16455，也就是说 0.3291
> 
> display _ data . desired _ content _ max _ luminance = 1

正如你所看到的，我们有相同的红/绿/蓝原色和白点，但我们没有关于亮度的信息。

就像在 AMD 上，要启用 HDR 模式，以`R16G16B16A16_FLOAT`格式创建交换链就足够了。我们可以全屏显示或者在窗口显示，没关系。在线性空间中也期望颜色，但是最大值似乎在值 12.5 左右(正好对应于 1000 尼特)。

就像之前一样，用`DXGI_COLOR_SPACE_RGB_FULL_G10_NONE_P709`调用`IDXGISwapChain3::SetColorSpace1`什么都不做，而其他值会让这个函数失败。我也试图调用`NvAPI_Disp_HdrColorControl`，但是在`NV_HDR_COLOR_DATA`结构中设置的值没有任何作用，甚至从`NV_HDR_MODE_UHDA` ( *)更改 hdrMode，“这是唯一支持的生产 HDR 模式。”*)到`NV_HDR_MODE_OFF`。HDR 在这件事上仍然很活跃。

# 3。GPU =英特尔虹膜加图形 655，驱动程序= 25.20.100.6444

`IDXGIOutput6::GetDesc1`返回了与 Nvidia 上相同的`DXGI_OUTPUT_DESC1`结构成员:

> BitsPerColor = 8
> 
> ColorSpace = DXGI _ COLOR _ SPACE _ RGB _ FULL _ g 2084 _ NONE _ p 2020
> 
> red primary、GreenPrimary、BluePrimary、WhitePoint 与 AMD
> 
> MinLuminance = 0 尼特
> 
> MaxLuminance = 1499 尼特
> 
> maxfullframegluminance = 799 尼特

各种显示模式的实验给出了非常有趣的结果:

*   当使用传统格式的交换链`R8G8B8A8_UNORM`且没有独占全屏时，值为 0..1 当然映射到 SDR，在 sRGB(伽玛)空间。
*   将交换链格式更改为`R16G16B16A16_FLOAT`时，仍处于窗口模式，HDR 开始工作。线性空间中的颜色是可预期的，最大亮度似乎在值 12.5 左右。
*   有趣的是，当我使用相同的 swapchain 格式`R16G16B16A16_FLOAT`，但使用功能`IDXGISwapChain::SetFullscreenState(TRUE, NULL)`进入独家全屏模式时，HDR 被激活，但最大亮度值已经达到 0.6 左右！
*   当使用 8 bpc swapchain 格式`R8G8B8A8_UNORM`进行全屏时，HDR 仍然处于活动状态，但这次最大亮度达到了值 1.0，sRGB 伽马空间是预期的。
*   就像之前的实验一样，用任何参数调用函数`IDXGISwapChain4::SetHDRMetaData`都没有效果。
*   不管传递了什么参数，函数`IDXGISwapChain3::SetColorSpace1`在 Intel 上总是失败。

# 结论

真是一团糟！有可能这里我还是有不明白的地方。我可能会更新这篇博文，或者以后再写一篇新的。目前在我看来，你可以在游戏中强力支持 HDR 显示器(这是个好消息)，但你需要忽略所有这些高级参数，只需遵循简单的步骤:

1.  使用`R16G16B16A16_FLOAT`格式的 swapchain。
2.  让玩家控制图形选项中的亮度和伽马指数，因为有效参数可能会因显卡而异(最大 HDR 亮度可能会达到 0.6 或 12.5)，而且没有好的方法来查询它们。