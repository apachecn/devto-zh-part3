# k 表示用 Dask(小猫图片的图像过滤器)聚类

> 原文：<https://dev.to/strikingloo/k-means-clustering-with-dask-image-filters-for-pictures-of-kittens-ip7>

对图像应用滤镜对任何人来说都不是新概念。我们拍了一张照片，对它做了一些修改，现在它看起来更酷了。但是人工智能从何而来？让我们用 K 均值聚类来尝试一下**无监督机器学习**的有趣用法。

我以前写过关于 **K 表示聚类**的文章，所以这次我假设你**熟悉**这个算法。如果你不是，这是我写的的[深度介绍。](http://www.datastuff.tech/machine-learning/k-means-clustering-unsupervised-learning-for-recommender-systems/)

我还用[自动编码器](http://www.datastuff.tech/machine-learning/autoencoder-deep-learning-tensorflow-eager-api-keras/)尝试了**图像压缩**(嗯，重建)，取得了不同程度的成功。

然而这一次，我的目标不是重建最好的图像，而是看看 T2 用最少的颜色重建图像的效果。

我不想让图片看起来尽可能地与原始图片相似，我只想让我们看着它说“真漂亮！”。

那么我们如何做到这一点呢？很高兴你问了。

## 如何用 K 均值聚类做图像滤镜

首先，记住一张**图像**只是一个像素的**向量总是好的。每个像素是一个由 0 到 255(无符号字节)之间的三个整数值组成的元组，表示该像素颜色的 RGB 值。**

我们希望使用 K 均值聚类来找到最能描述图像特征的 *k* **颜色****。这只是意味着我们可以将每个**像素**视为一个**单个数据点**(在三维空间中)，并对它们进行聚类。**

所以首先，我们想用 Python 将一幅图像转换成一个像素矢量。我们是这样做的。