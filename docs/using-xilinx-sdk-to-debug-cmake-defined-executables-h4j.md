# 使用 Xilinx SDK 调试 CMake 定义的可执行文件

> 原文：<https://dev.to/zhu48/using-xilinx-sdk-to-debug-cmake-defined-executables-h4j>

# 事情

在艰难地克服了许多障碍之后，我现在可以使用 Xilinx SDK 来调试通过 CMake 脚本定义的可执行文件。

# 背景

在本系列的前几篇文章中，我总结了 [Alpha Automata](https://github.com/AlphaAutomata) 项目以及我使用 CMake 生成 Xilinx SDK 项目的历程。

我想要 CMake 的依赖管理和自动化，我想要使用 Xilinx SDK 的调试器。因此，我必须想办法在 Xilinx SDK 的调试器中运行使用 CMake 生成的构建系统编译的可执行文件。在过去的三周里，我花了大量的空闲时间来思考如何做到这一点。

# CMake 遇上 Xilinx 系统调试器

三周前，我已经知道如何使用 CMake 生成 Xilinx SDK 项目。本系列的最后一篇文章描述了这一旅程。

### CMake 隐形

我可以导入 SDK 项目，并编译和链接和`elf`文件。然而，Xilinx SDK 不允许我为可执行文件设置调试配置文件。

[![no_project](img/7bb0903dde3aa46fb667072dc99496d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xpZ7vqB3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/exj96thxfmvih7ca5zo4.PNG)

如您所见，Xilinx SDK 拒绝承认我的导入项目的存在。

我花了很长时间在谷歌上搜索，没有得到满意的答案。事实上，我没有发现很多人甚至在谈论将 CMake 和 Xilinx SDK 混合在一起。由于缺乏信息，我开始尝试。我使用 Xilinx SDK GUI 创建了应用程序项目，并将这些项目的配置与我导入的 CMake 生成的项目的配置进行了比较。几乎是偶然的，我找到了我需要勾选的复选框:

[![proj_ref](img/49c9f0cf4d5261b2ae2a1455e467f287.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wP4XcjP---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3yfp1llen1nd20ds98y5.PNG)

导入的项目必须设置为引用导入项目的`Project Properties`中的目标 SoC 的板卡支持包项目。选中这个复选框后，Xilinx SDK 确认我导入的项目是调试概要文件的候选项目。

[![yes_project](img/72d7e0d63c6c5965d64382bc319513e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--23janPEf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kvzntg77h3ctli7p6bct.PNG)

最后，我可以指定使用导入的 CMake 生成的项目编译的可执行文件用于调试。

[![executable](img/b6c9031c6058e704c2c43ef27614269e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EJSxso78--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sftvk5qc5hmts3ovejgn.PNG)

此时，我欣喜若狂。就像软件中的许多事情一样，寻找解决方案花费了太长的时间，而结果证明解决方案太简单了。

### CMSIS vs Zynq

我高兴地点击`debug`按钮，在目标 SoC 上运行我的可执行程序...

[![load_err](img/606e5fd251559e976f7de69300ade6db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eg18dF0j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7gkzw1doldnd4clve1px.PNG)

...Xilinx SDK 也同样开心地朝我脸上吐口水。

我还查找了错误中调用的地址，看看它是否是我的特定芯片访问的合法地址，答案是一个响亮的“也许”。

[![addr](img/0713fb54fe002489a48399a5689381ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pwNp7Qr6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jsc93b3qva52n39x24y4.PNG)

根据 Xilinx 文档，地址`0x8000_0000`标志着可编程逻辑存储器区域的开始。在其他作品中，这是用于读写 FPGA 逻辑的地址范围之一。由于我没有在我的 Zynq SoC 的 FPGA 结构中为这个调试会话实现任何东西，我的代码当然不应该访问 FPGA 结构内存范围。碰巧我使用的 CMSIS 链接器脚本将地址`0x8000_0000`指定为 ROM 的开始。

[![cmsis](img/b59e2bd4a3ce78660656cb0aeac22e86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wXBRLRmj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w09rpapmx0nqimb9567g.PNG)

此时，我对自己呻吟着。Xilinx SDK 自动为使用其 GUI 创建的应用程序项目生成链接器脚本。每当我为我的 CMake 生成的项目点击`Generate linker script`按钮时，Xilinx SDK 拒绝做任何类似的事情。在一次大胆的尝试中，我没有指定任何链接器脚本，只是让 GCC 选择它想要放置所有二进制块的位置。

[![debug](img/e9eafda640912712c9d7bfd8308a32e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--za5Avgfp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n6df96j6wujed1ue4w8d.PNG)

而且成功了。我对 GCC 和 ARM 一贯的创业行为有了全新的认识。

*所有 ARM 处理器立即从地址为`0x00000000`或`0xFFFF0000`的向量表中获取初始堆栈指针和初始程序计数器，这取决于具体的处理器。可以使用矢量基址寄存器(VBAR)移动该向量表。*

# 阿尔法的状态

自上个月以来，情况没有太大变化。我真的花了三周时间让 CMake 和 Xilinx SDK 互相配合。

# 向前及向上

现在，我有了一个运行在真实硬件上的调试器，我希望在实现 RTOS 方面取得更快的进展，并花更少的时间来使用我的工具。

我说过我会努力每两周发一次帖子，我做到了。我真正的工作开始有起色，感恩节和我的未婚夫合谋让我社交。希望我能在圣诞节和我未婚夫绑架我之前再发一个帖子。