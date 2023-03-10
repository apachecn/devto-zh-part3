# k-最近邻算法

> 原文：<https://dev.to/devtox/k-nearest-neighbors-algorithm-44aj>

“告诉我谁是你的朋友，我会告诉你你是谁”。这就是 k-最近邻算法的思想基础。

您可以在向量空间中表示数据。这可能是长度(x)和重量(y)，大小(x)和价格(y)或任何你想要的。

给定一个二维空间，它寻找 k 个最近的邻居。空间中的任何点都可以是组/类的一部分。意味着最接近的点可能告诉该类一个新点。

[![](img/284cb4126d3ba7ed2724802c85cc0de7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5vzEKmJz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pythonbasics.org/wp-content/uploads/2019/03/knn.png)

在上图中，有一个红蓝组。新的黑点最有可能属于红色组，因为它被红色邻居所包围。

阅读更多: [k 近邻](https://pythonbasics.org/k-nearest-neighbors/)