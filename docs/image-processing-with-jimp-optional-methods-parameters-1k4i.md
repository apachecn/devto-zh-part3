# 用 Jimp 进行图像处理。可选方法参数。

> 原文：<https://dev.to/kubaapk/image-processing-with-jimp-optional-methods-parameters-1k4i>

嗨伙计们。

我在做一些云图像的托管和处理。我确实用了 Jimp，它很棒，非常管用。

Jimp 给出了很多很酷的图像处理方法，比如调整大小，缩放等等。您可以链接方法以应用多种效果，例如:

```
Jimp.read('image/path')
 .then(image => {
     image
        .resize()
        .scale()
        .grayscale()
 })
 .... 
```

#### 我想实现什么

我想快速处理图像。例如，我有一个网址:

`[...].images.io/i/image-uuid?list=a&of=b&params=c`

这些参数可以是宽度、高度、比例、旋转等。

现在我可以通过给定一个特定的参数 f . e .来操作图像。

`[...].images.io/i/image-uuid?scale=0.5`

这是一个作品——它是图像缩放。

但是我想有机会在一个请求中使用一堆选项，例如:
`[...].images.io/i/image-uuid?scale=0.5&filter=grayscale&rotate=80`

我不知道如何让它工作。目前，Jimp 提供了 30 多种方法，这些方法给了我们一堆组合，所以简单的开关盒无法工作。；)

根据文档，没有像可选参数这样的东西，并且您不能将 nulls 或 undefined 作为方法参数传递。

有什么办法可以克服这一点吗？:)

大家节日快乐！