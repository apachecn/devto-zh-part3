# 在 JS 中创建图像过滤器

> 原文：<https://dev.to/mebble/image-processing-using-image-kernels-in-js-5550>

我花了两周时间在一个项目上，我希望这个项目能帮助我具体理解图像内核是如何工作的。你知道 Photoshop 或典型的照片编辑器应用程序中的图像过滤器可以让你锐化或模糊图像吗？这些都是使用图像内核实现的。这篇[博客文章](http://setosa.io/ev/image-kernels/)是迄今为止我能找到的关于图像内核的最好的解释。更正式一点的，这里是[维基百科](https://en.wikipedia.org/wiki/Kernel_(image_processing))。

演示在网上，我在下面给了它的链接。你会看到一个简单的页面，顶部有一些说明。请随意使用内核和示例图像。

图像被分成四个分区，每个分区被传递给一个 web worker 进行处理，这样我们就不会阻塞主线程。我发现四个网络工作者处理整个图像的速度仅仅是一个网络工作者的两倍多一点。我期望四倍的速度，但我想我太乐观了，没有考虑复制像素阵列的开销等。

我可以断定，我现在对图像内核的工作方式有了更好的理解(除了我花了更多的时间和 web 工作者一起工作)。要是这能给我的图像处理课程加分就好了。

这是演示版和资源库

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [梅布尔](https://github.com/mebble) / [ imfx](https://github.com/mebble/imfx)

### 通过核卷积对图像应用滤镜

<article class="markdown-body entry-content container-lg" itemprop="text">

# ImFx

执行客户端图像处理算法，如:

*   空域滤波
*   位平面切片

查看[演示](https://mebble.github.io/imfx/)。

## 它的作用

*   拍摄图像
*   需要一个内核
*   对图像应用核卷积
*   显示输出

## 它看起来像什么

[![Kernel for image sharpening](img/df6c80b543853ae9f0bc7eacb2ccb8b4.png)](https://github.com/mebble/imfxdocs/sharpen-kernel.png)

*用于图像锐化的内核*

[![Kernel for edge detection](img/0d53034e50b0a67dbb8f456b39d24a62.png)](https://github.com/mebble/imfxdocs/edge-kernel.png)

*用于边缘检测的内核*

[![Sharpening an image of a cat in a hat](img/fc374d737810e654b87a35b0c36f61d4.png) ](https://github.com/mebble/imfxdocs/cat-example.png) *锐化一只戴着帽子的猫的图像* *(图片来源:[rawpixel.com 来自 Pexels ](https://www.pexels.com/photo/grey-chartreaux-cat-with-red-and-white-party-hat-and-licking-nose-1663417/) )*

[![Edge Detection on an image of skeletons on the street](img/4e121f9d362ec1949e8d13b161435e90.png)](https://github.com/mebble/imfxdocs/bones-example.png)

## 托德斯

*   从模板中选择可更新的内核
*   从一系列图像中进行选择
*   在 4 名工人之间分割和处理图像
*   记录处理图像所花费的时间。可以记录每个工人分别花费的时间
*   实现更多图像处理的东西
    *   最小值/最大值/中值过滤器
    *   用于更快卷积的可分离滤波器
    *   位平面切片
*   使用 WebGL 在每个工人中进行快速处理
*   从设备上传图像
*   拍摄相机照片并使用图像
*   …

</article>

[View on GitHub](https://github.com/mebble/imfx)