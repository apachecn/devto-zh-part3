# 如何为 Vulkan 设计一个库的 API？

> 原文：<https://dev.to/reg__/how-to-design-api-of-a-library-for-vulkan-hgd>

在我昨天的上一篇博文中，我分享了我对图形 API 和库的想法。另一个让我产生这些想法的问题是一个问题:**如何使用 Vulkan 或 DX12 为实现单个算法、过程或图形效果的库设计一个 API？**乍一看，这似乎很琐碎，就像一项需要设计和实施的任务，但如果你仔细想想，就会发现这是一个困难的问题。现有的像这样的软件库很少。我在这里指的不是一个复杂的库/框架/引擎，它“水平地”包装了整个图形 API，并把它带到了一个更高的层次，就像 [V-EZ](https://github.com/GPUOpen-LibrariesAndSDKs/V-EZ) 、 [Nvidia Factor](https://developer.nvidia.com/falcor) ，或者 [Google Filament](https://github.com/google/filament) 。我的意思是，只是一个小的，“垂直的”，插件库做一件事，例如，实现环境遮挡效果，有效的纹理贴图下采样，渲染用户界面，或在 GPU 上模拟粒子物理。这样的库需要与用户代码的其余部分有效地交互，以成为大型程序或游戏的一部分。 [Vulkan 内存分配器](https://github.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator/)也不是一个很好的例子，因为它只管理内存，不实现渲染过程，不涉及着色器，并且它只在与内存碎片整理相关的部分与命令缓冲区交互。

我在工作中遇到了这个问题。后来我也和同事详细讨论过。有多个问题需要考虑:

*   谁为缓冲区和纹理/图像分配内存？有两种类型——与用户代码交互的输入/输出类型和用于中间结果的内部类型。用户应该创建它们吗？如果是这样的话，我们必须告诉他这些资源的需求，比如大小和所需的最少的一组`VK_IMAGE_USAGE_`标志。如果图书馆应该在内部分配它们，它应该如何做？仅仅通过抓取新的`VkDeviceMemory`块？如果用户更喜欢使用自己选择的复杂分配器来分配所有 GPU 内存，比如 [Vulkan 内存分配器](https://github.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator/)，会怎么样？
*   谁分配必要的描述符？像内存资源一样，库可以创建自己的内部`VkDescriptorPool`，但是如果用户更喜欢从自己的描述符池中分配所有的描述符呢？
*   对于所有涉及的资源，渲染之前和之后的障碍由库提供，由谁传递？应该是用户，还是图书馆？障碍是用户代码和库代码之间的一个明显的接触点，因此必须有一种方法来发布它们，同时考虑源和目标图像布局、管道阶段和访问标志。
*   如何提供着色器的 SPIR-V 代码？库应该在内部从文件中加载它，还是由用户负责从他想要的任何来源加载它们，并在库初始化时将指针传递给它们的内存？也许我们可以以某种方式将它们捆绑到库源代码中，例如，作为自动生成的 C++代码中定义的大型常量字节数组？
*   如果用户不想静态地链接到 Vulkan 函数，而是使用`vkGetDeviceProcAddr`获取指向其函数的指针，可能借助类似 [volk](https://github.com/zeux/volk) 的东西，该怎么办？图书馆应该可以使用它们。
*   如果用户想把自定义的 CPU 分配回调(`VkAllocationCallbacks`)传递给所有 Vulkan 函数怎么办？图书馆应该可以做到。

这是一个类似于任何 C++库的问题。对于各种基本设施的实现，如字符串、容器、断言、互斥等，还没有达成一致意见。，所以各大框架或者游戏引擎都是自己实现的。甚至像最小值/最大值函数这样简单的东西也被定义在多个地方。在`<algorithm>`头定义了一次，但是有些开发者不用 STL。`<Windows.h>`提供了它自己的，但是这些被定义为宏，所以它们破坏任何其他的，除非你在 include 之前加上`#define NOMINMAX`……一个典型的 C++噩梦。较小的库最好是可配置的，或者定义自己的一切，比如 Vulkan 内存分配器有自己的[断言](https://github.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator/blob/65f096555d68c11fbefc11b7561189c70f0ce183/src/vk_mem_alloc.h#L3227)、[向量](https://github.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator/blob/65f096555d68c11fbefc11b7561189c70f0ce183/src/vk_mem_alloc.h#L3946)(可以切换到标准 STL 版本)，以及 [3 个版本的读写互斥](https://github.com/GPUOpen-LibrariesAndSDKs/VulkanMemoryAllocator/blob/65f096555d68c11fbefc11b7561189c70f0ce183/src/vk_mem_alloc.h#L3320)。

所有这些问题使得开发人员更容易写一篇论文，描述他们的算法，可能共享一段代码，伪代码或着色器，而不是提供现成的库。这是一个非常糟糕的情况。我希望随着时间的推移，使用 Vulkan/DX12 实现单次传递或效果的库的 API 应该是什么样子。最近，我的同事与我分享了一个想法，如果有一些更高级别的 API 来实现各个部分(如资源分配、图像屏障)之间的所有这些交互，并且我们都同意使用它，那么创作库并在其上将它们缝合在一起将会容易得多。这是需要这种新的、更高级图形 API 的另一个理由。