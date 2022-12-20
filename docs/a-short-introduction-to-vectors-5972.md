# 向量简介

> 原文：<https://dev.to/brandonskerritt/a-short-introduction-to-vectors-5972>

### 什么是矢量？

向量是一个容器，其中顺序很重要，并且允许重复。一个 N-vector 有 N 个分量(元素)，每个分量称为不同的值，在一个 vector 中可以是相同的，所以:

[![A Short Introduction to Vectors](img/ae32189429b47c9a2b882c262ccf8ee8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--glKX_Z9V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ANi5sW5wzKXtXj1KvLrh5mw.png)

用向量是完全可能的。

[![A Short Introduction to Vectors](img/cf5877fe826b94a0c165db7803fec655.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IQVojl_n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AjNFBSGysOBxgS0I6vEaALg.png)

二维向量代表二维空间，三维向量代表三维空间。

### 用向量做数学

两个向量可以这样相加:

[![A Short Introduction to Vectors](img/835244ec0ebfdd470a13c4c77fc10baf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U_bYuGAQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A1qCtyQtRl5pE253StarNrg.png)

结果是一个新的向量，称为 w。

如果向量具有相同数量的分量，则只能添加两个向量。

标量乘是将向量中的每一项增加 R。设 R 是一个实数，则:

[![A Short Introduction to Vectors](img/6fa428e181f73bbcddc77fb9b77f61b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LtnOl7yp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A4rxXRqaTEPWNkPQH9_9cOg.png)

向量长度可以用基数来定义。向量 A 的长度是|A|。向量的长度使用以下公式:

[![A Short Introduction to Vectors](img/d07c2706ee13feed2386e61c0956ec4d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dVqrDTrx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AcLhJUkS6A7OjRmGKYk4KLw.png)

如果你试图计算一个负向量的长度，你会得到正向量的长度。

[![A Short Introduction to Vectors](img/a8ff78b295f4235c89cdce15c31d2b56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VZePb1f4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AuW79OVDXNYCXVPyQeBlaSw.png)

另一件需要注意的事情是:

[![A Short Introduction to Vectors](img/fc1940e284cebd595e5821c76d601ad7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jiHUFQPd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AWs8lbvI2bySYZ4ujiT-fMw.png)

为了反转一个向量，你将每个分量乘以-1，使所有的都是负的，并且

[![A Short Introduction to Vectors](img/e97790ac6f581fff60fb31de897a0bd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CjygdyUg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AummIijJ6Gj89wKitnkO46Q.png)

### 向量空间

从一些数字开始，如实数、整数、整数、有理数等。

[![A Short Introduction to Vectors](img/837ea43d2ddffd3e6fc15866547dd918.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TU5PhwHW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A7QwBJhyNjgkDgnAQeFjk3w.png)

一个数集合上的上标数，例如是所有实值(实数中的数)n 向量的集合，其中每个分量都在实数集合中。