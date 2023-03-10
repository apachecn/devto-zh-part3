# 图像处理的 matlab 拉普拉斯算法

> 原文：<https://dev.to/berkayakcay/image-processing-matlab-laplacian-algoritmasi-pke>

[![](img/445bcddfefb49fb16d168fe033eabd87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8ITjlHYe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/756/1%2AloRz3J48jW5XEpr-MieSXg.jpeg)

本例使用了 3×3 滤镜和黑白图像。

### 拉普拉斯算法

我们的目标是：

*   以像素为单位读取您的图片。
*   将每个像素周围 n 距离的像素值乘以指定的核心矩阵。(可以使用“正”或“负”内核按预期向内或向外绘制边缘。)
*   指定新图像中同一位置的像素的平均值。