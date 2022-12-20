# 制作特色图片就像叛徒编码器

> 原文：<https://dev.to/renegadecoder94/make-featured-images-just-like-the-renegade-coder-3gpf>

最近， [@hmajid2301](https://dev.to/hmajid2301) 向我的[图像标题 repo](https://github.com/TheRenegadeCoder/image-titler) 请求将 Python 脚本转换成 pip 包。自然，我想花些时间给这个项目更多的爱。让我们学习如何像叛徒编码器一样制作特色图片。

## 什么是 image-titler？

不久前，我和妻子一起创建了一个模板，我可以用它来为我的网站创建特色图片。最终，我们选定了双红色条模板，您可以在本文顶部的实践中看到它。

为了使用该模板，我过去必须执行以下操作:

1.  下载一张图片
2.  将图片上传到在线图像编辑器
3.  应用模板
4.  替换模板文本
5.  调整红色条的大小
6.  摆弄定位，直到满意为止

当然，我发现重复的任务令人厌倦，所以我必须找到一种方法来自动化上述过程。因此，我求助于 Python，看能否制作一个小脚本来自动应用模板。结果是我称之为图像标题的东西。

## image-titler 是如何工作的？

image-titler 脚本的工作原理是，通过最接近中点的空间分割图像标题，然后将两个子字符串映射到红色块上，并在看到这些块时定位它们。例如，这篇文章的标题是“像叛逆的编码者一样制作特色图片”通过离中点最近的空间进行分割会产生“使特色图像公正”和“像叛徒编码器一样”:

```
>>> from image_titler import trc_image_titler
>>> trc_image_titler.split_string_by_nearest_middle_space("Make Featured Images Just Like The Renegade Coder")
('Make Featured Images Just', 'Like The Renegade Coder') 
```

Enter fullscreen mode Exit fullscreen mode

总之，这里有四个函数在起作用:`main`、`split_string_by_nearest_middle_space`、`draw_text`和`save_copy`。像往常一样，`main`函数读入选项，并基于这些选项运行图像命名过程。其他三种方法充当整个脚本的业务逻辑。

令人惊讶的是，整个脚本有 135 行代码，包括文档、导入和常量。换句话说，有很多机会让这个脚本成为你自己的。为什么[不通过回购](https://github.com/TheRenegadeCoder/image-titler)提出拉取请求？

## 如何使用 image-titler？

由于 image-titler 现在是一个 pip 包，使用它非常简单:

```
pip install image-titler
image_titler 
```

Enter fullscreen mode Exit fullscreen mode

从那里，该程序应该启动一个文件输入窗口，要求您的图像。一旦您选择了您的文件，它将尝试解析文件名并生成标题。特别是，该脚本要求文件名用连字符分隔。例如，这篇文章的特色图片有如下文件名:make-featured-images-just-like-the-renegade-coder.jpg。

如果您想要更多的控制，有三个选项:

*   -o:设置输出路径
*   -t:设置要放在图像上的标题
*   -p:设置输入文件的路径

例如，下面的代码片段运行带有固定标题的 image-titler:

```
image_titler -t "Make Featured Images Just Like The Renegade Coder" 
```

Enter fullscreen mode Exit fullscreen mode

从那里，您将被提示选择一个图像文件，结果将被转储到工作目录。

## 如何帮助 image-titler？

如前所述，我很乐意让其他人看看他们是否能找到脚本的用处。目前，它基本上是按照我的风格硬编码的，但我相信你可以根据自己的需要扩展它。一个有趣的开始可能是通过可选参数来配置条形图的颜色。此外，我希望能够缩小图像，使裁剪工作正常。

如果这个项目听起来很有趣，我推荐[看看源代码](https://github.com/TheRenegadeCoder/image-titler/blob/master/image_titler/trc_image_titler.py)。如果我对互联网和编码社区有所了解的话，那就是我肯定会找到不喜欢我的代码的人。那么，为什么不试一试呢？

当您在这里时，您可能也有兴趣帮助 Renegade Coder 社区扩展[样例程序 repo](https://github.com/TheRenegadeCoder/sample-programs) 。它是尽可能多的语言的代码片段的集合。目前，我们希望在文档方面得到一些帮助。许多代码需要文档化。

无论如何，谢谢你过来！如果你知道谁有兴趣帮忙，与他们分享这篇文章。我会感谢你的支持！