# 树莓派，天蓝色，还有一个 1970 年代的油罐

> 原文：<https://dev.to/csharpfritz/raspberry-pi-azure-and-an-oil-tank-from-the-1970s-10h3>

我家里有一个问题，我已经处理了很多年了:我有一个旧油箱，它是房子附带的，我无法处理掉。它位于我的地下室，管道穿过地基，比我的地下室门还大。从字面上看，水箱是在房子建成之前安装的。我们定期在深秋、冬季和早春月份提供生物柴油燃料来加热房屋。这个来自 20 世纪 70 年代的人工制品让我烦不胜烦，因为我需要监控一个模拟仪表来知道什么时候为我的家重新订购石油。

但是…我是软件开发人员！我知道云，我想我知道很多。我想造一个装置来简化这个庞然大物的管理。我在 2017 年夏天有了这个最初的想法，[写了一篇文章，讲述了我最初的实验，以及使用 LEAD Tools OCR 产品分析储罐的量规并报告储罐的“真实体积”](https://www.codeproject.com/Articles/1203445/Updating-s-Technology-with-the-Help-of-s-Imaging-2)。这些最初的实验是有效的，但是要让整个系统运转起来还需要太多的东西。我需要一种更简单、更小巧的方法…

## 计划..

我曾提议与我的好朋友 [Suz Hinton](https://noopkat.com) 为[deviintersection Las Vegas 2018](https://devintersection.com)举行一次更具互动性的结对编程会议。我们不知道我们可以一起编写什么，她在物联网、节点、JavaScript 和云方面的专业知识与我在所有 C#方面的专业知识相结合。我的思绪回到了这个概念，这个我一直在处理的问题，我们策划了一个阴谋。Suz 会为 Pi 编写代码来拍照并上传到 Azure，我会用 C#编写 Azure 函数来分析照片。幸运的是，我们的朋友 [bravecobra](https://github.com/bravecobra) 制作了这个流程图来记录处理过程:

<figure>[![Oil Tank Vision Flow chart](img/d8a3e929c694ed39f614ce6a3b5d2f3f.png)](https://github.com/csharpfritz/OilTankVision/blob/a747fb6898078b52248b184c1e65cd9c1e4d4736/docs/OilTankVision-Flowchart.pdf) 

<figcaption>通过数据分析处理从照片抓拍的数据</figcaption>

</figure>

下面是如何工作的快速版本:

1.  在预定的基础上，使用 Linux cronjob，Raspberry Pi 将拍摄仪表的照片
2.  Pi 会将照片上传到 Azure blob 存储容器
3.  照片上传将触发 Azure 功能，并将照片发送到 Azure 认知服务进行分析
4.  照片分析的结果将报告量表窗口中的数字，我们将把它存储在 Azure 表存储记录中，以供将来分析

有趣的部分来了…您可以观看我们讨论并构建这一组初始服务。下面是我们的结对编程会话。我是戴帽子的那个

<figure>

[https://www.youtube.com/embed/gzPyMkiYdu4](https://www.youtube.com/embed/gzPyMkiYdu4)

<figcaption>Building the initial OilTankVision services while on-stage at DevIntersection with Suz Hinton</figcaption>

</figure>

我们在 GitHub 上提交了我们的[源代码，这样当我回家时，它可以被修补并完成初始部署。在接下来的一两周内，在我的一个流中，我用 Raspian Linux 的最新版本更新了 Pi，并部署了代码来拍照。那个](https://github.com/csharpfritz/oiltankvision)[脚本在我们的 GitHub 库的 PiScript 文件夹中，](https://github.com/csharpfritz/OilTankVision/blob/master/PiScript/uploadBlob.js)这里有一个片段: