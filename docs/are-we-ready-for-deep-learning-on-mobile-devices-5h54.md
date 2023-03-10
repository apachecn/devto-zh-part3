# 我们准备好在移动设备上进行深度学习了吗？

> 原文：<https://dev.to/brightdevs/are-we-ready-for-deep-learning-on-mobile-devices-5h54>

由于深度学习可能性的快速增长，许多聪明人尽最大努力创建越来越复杂的算法，这些算法在许多领域超越了人类的表现。幸运的是，他们的大部分工作都是公开的，等待你以某种奇特的方式利用它。

[![](img/491e8e449e4b6e8b1ed4c5f3fda975b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vJUosRMQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x1f7gp7k8qr03y2lwnsm.jpg)

利用它的一种方法是在拥有大量数据源(如移动电话及其传感器)的环境中对预训练的模型进行推断。你可能想知道，如果你可以轻松地利用互联网连接和来回发送数据，为什么还要在移动应用程序上进行推理，但我们必须记住，这不是非常节能的解决方案。更有说服力的是，因为我们在移动设备上进行推理，所以我们可以离线工作，这也导致了低延迟。最后但并非最不重要的是隐私，有时我们只是不想向外发送任何数据。

不幸的是，在移动设备上也有一些抖动。最重要的问题是相对较少的计算机能力和严格的内存限制，这阻碍了大型模型的有效使用(或根本不使用)。

## TensorFlow Lite 进行救援！

使用 TensorFlow Lite ,您可以在各种设备上轻松使用您的模型，如 Android、iOS 或 Raspberry Pi。坚持下去，直到你了解 TensorFlow 如何加速一些耗时的任务。它使用了[flat buffer](https://google.github.io/flatbuffers/)——一个跨平台的库，允许访问序列化的数据，而无需预先解析，并保留数据结构，以接近原始结构访问的难以置信的速度实现。

另一件事是在 ARM 上为 NEON 做了额外的内核优化。此外，它可能通过直接 GPU 集成或 [Android 神经网络 API](https://developer.android.com/ndk/guides/neuralnetworks) 来提高速度。

下一个至关重要的组件是[训练后量化](https://www.tensorflow.org/lite/performance/post_training_quantization),它可以减少模型大小，同时将延迟降低 3 倍，精确度损失最小。

此外，在 2019 年 tensor flow Dev Summit 上，我们可以了解 TensorFlow Lite 的一些快速扩展的用例，以及如何更快地做出推断。

TensorFlow Lite 已经增强了许多应用程序，你可能已经利用了 android 相机上的人像模式、*嘿，谷歌助理中的谷歌*或谷歌照片等功能。

现在我们来总结一下在移动设备上使用 TensorFlow 模型的全过程。

*   创建模型或获取现有模型
*   将模型转换为。tflite 格式
*   将转换后的模型保存在您的设备上
*   加载模型并做出推断

正如你所看到的，你可以通过以下四个主要步骤来实现它。然而，机器学习尤其是深度学习背后有很多机会，所以为什么不利用它呢。有一大堆的事情要涵盖，但我相信这是值得你花时间。借助 TensorFlow/Keras 和 TensorFlow Lite 等优秀工具，您可以比以前更轻松地将当前模型部署到移动设备上。敬请关注下一篇关于使用 TensorFlow Lite 创建 Android 应用程序的帖子！

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者拉多斯瓦夫·索维斯基，软件工程师@光明发明
[电子邮件](//radek.slowinski@brightinventions.pl)