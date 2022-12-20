# 用一行命令训练自己的图像分类器

> 原文：<https://dev.to/0xkoji/train-own-image-classifier-with-one-line-command-1ho8>

最近，我发布了一个 python 模块，icTrainer，允许用户轻松训练图像分类器。

icTrainer 有 3 个功能。

## 收集图像

此功能允许用户轻松收集图像。然而，这个函数有几个问题。第一种是收集零字节图像(但是零字节文件在完成下载过程后被移除),并且该功能收集的图像不是训练图像分类器的最佳图像，因此用户需要手动选择图像。然而，我认为当人们想从网上快速下载图片时，这个功能还是很有用的。

```
$ ictrainer --mode collect --keyword dog -n 250
$ ictrainer --mode collect --keyword cat -n 250 
```

Enter fullscreen mode Exit fullscreen mode

## 调整图像大小

ImageResizer 提供了图像大小调整功能。该函数用于 ImageCollector 下载的图像(第一个函数)，因此需要遵循`dataset/image_folder_name`结构。(这个问题将很快得到解决)

```
$ ictrainer --mode resize --target cat --image_width 480 --image_height 320 
```

Enter fullscreen mode Exit fullscreen mode

## 训练图像分类器

然后，最后一个功能是训练一个 keras 图像分类器。
输出为`.h5`。用户可以用测试脚本(【https://github.com/koji/icTrainer/tree/master/check_model】T2)尝试自己的分类器。
该功能使用 vgg16 进行微调。

```
$ ictrainer --mode train --classes "cat" "dog" --mname "dogAndcat_" 
```

Enter fullscreen mode Exit fullscreen mode

我计划增加人脸检测和人脸裁剪功能，用于训练人脸分类器。

详情在此
[https://github.com/koji/icTrainer](https://github.com/koji/icTrainer)
【https://pypi.org/project/ictrainer/】T4