# 使用 TensorFlow.js 将机器学习引入浏览器—第二部分

> 原文：<https://dev.to/vabarbosa/bring-machine-learning-to-the-browser-with-tensorflowjspart-ii-1e3i>

> *从[medium.com](https://medium.com/ibm-watson-data-lab/bring-machine-learning-to-the-browser-with-tensorflow-js-part-ii-7555ed9a999e)T3】传中*
> 
> ***编辑于 2019 年 3 月 11 日**包含 TensorFlow.js 1.0 中引入的变更。关于 TensorFlow.js 1.0 更新的更多信息可以在[这里](https://gist.github.com/caisq/3fc0beb6597f42d66be806c6692f310d)找到。*

#### 将预先训练好的模型转换成网页友好格式

<figure>[![](img/3e3bd340e13c000e4c24ac88c97ee1ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YOokt3sg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQwZ1EoUo6slEwlJVpNwTnw.jpeg) 

<figcaption>杂色叶乘[克里斯·劳顿](https://unsplash.com/@chrislawton?utm_source=medium&utm_medium=referral)上</figcaption>

</figure>

如果您已经跟随学习了[，那么您应该已经对如何将一个预先训练好的模型引入浏览器应用程序有了很高的理解。现在，让我们更详细地看看这个过程的第一步。](https://dev.to/vabarbosa/bring-machine-learning-to-the-browser-with-tensorflowjspart-i-4f4m-temp-slug-2078470)

在将预先训练的模型转换为 web 友好格式并将其带到浏览器之前，您首先需要一个模型。从[模型资产交易所](https://developer.ibm.com/code/exchanges/models/)的[图像分割器](https://github.com/IBM/MAX-Image-Segmenter)开始学习是一个很好的第一个模型。您可以通过 Kubernetes 或 Docker Hub 部署和运行 Image Segmenter 模型。为了了解它的功能，你可以看看[尼克·卡斯滕](https://medium.com/u/6c8f3f54d771)的[魔法裁剪工具](https://developer.ibm.com/patterns/max-image-segmenter-magic-cropping-tool-web-app/)。

### 获取模型

您可以从下载并提取 MAX 图像分割器中使用的[模型文件](http://max-assets.s3-api.us-geo.objectstorage.softlayer.net/deeplab/deeplabv3_mnv2_pascal_trainval_2018_01_29.tar.gz)开始。提取的内容包含冻结的模型图。[冻结图形](https://cv-tricks.com/how-to/freeze-tensorflow-models)将所有需要的模型数据封装在一个文件中(。pb 扩展)。

> TensorFlow.js 转换器支持 Keras(即 HDF5)和 TensorFlow(如冻结图、SavedModel)模型。

您可能会遇到的一些其他模型格式包括:

*   [检查点](https://www.tensorflow.org/guide/checkpoints)，包含保存模型当前状态所需的信息。然后加载检查点后恢复训练。转换器不支持检查点。
*   [SavedModel](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/python/saved_model/README.md) 是 TensorFlow 的通用序列化格式。与检查点不同，SavedModels 以语言中立的格式存储模型数据。
*   [HDF5](https://en.wikipedia.org/wiki/Hierarchical_Data_Format) 这是 Keras 用来存储模型数据的格式。这是一种用于存储多维数字数组的网格格式。

### 知道模型

在使用模型之前检查和理解它是一个好的习惯。您不需要了解模型的每个细节，但是了解[模型的格式](https://www.tensorflow.org/extend/tool_developers/)、输入和输出是一个很好的开始。

> 在大多数情况下，您必须预处理模型的输入，以及处理模型的输出。

通过检查模型的图形，了解模型的输入、输出和操作。一个用于查看机器学习模型的有用且易于使用的可视化工具是 [Netron](https://github.com/lutzroeder/Netron) 。

要检查图像分割器模型，在 Netron 中打开提取的`frozen_inference_graph.pb`文件。您可以缩小以查看模型的范围和大小。同样，您可以放大图形上的特定节点/操作。

在不点击任何节点的情况下，点击汉堡包/菜单图标来查看模型的属性(例如，操作数、输入类型等。).此外，单击特定节点可以查看属性。或者，您可以输入`CTRL+F`来打开搜索面板，并键入特定的节点来跳转到它。

<figure>[![](img/d54ee1b09ac2959e3d89b0e0e34d1fff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y-hChKK9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A4RfJ7WTT6LBaSB0AivsyWg.png) 

<figcaption>图像分割器模型属性</figcaption>

</figure>

图像分割器的输入是类型为 **uint8[1，,?**，3。这是一个 8 位无符号整数值的四维数组，形状为 **1，？,?，3** 。**？s 是占位符，可以代表任意长度。它们将对应于图像的长度和宽度。 **1** 对应于批次大小，而 **3** 对应于给定像素的 RGB 值的长度，这是三个数字。**

点击最后一个节点(`Slice`)，得到它的名称(即`SemanticPredictions`)和属性。记住这个名字很重要。您需要将它提供给转换器工具。

<figure>[![](img/ce72e0135b36dfac5eb55cf0b8cde48c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YVCyiLsT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADGiFs1kbq0JIJjMxb2NFBQ.png) 

<figcaption>图像分段器节点属性</figcaption>

</figure>

查看图表信息的其他可用选项有[summary _ graph](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/tools/graph_transforms/README.md#inspecting-graphs)和 [TensorBoard](https://www.tensorflow.org/guide/graph_viz) 工具。

### 改变模式

您现在可以运行 [tensorflowjs_converter](https://github.com/tensorflow/tfjs-converter) 来获得您的 web 友好格式。

> 安装完`tensorflowjs` Python 包后，可以通过命令行使用转换器

要转换图像分割器，请指定:

*   **语义预测`output_node_names`的**
*   **tf_frozen_model** 为`input_format`
*   冻结图形的文件路径
*   存储转换模型的目录路径