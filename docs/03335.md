# 一个优雅的虚拟列表组件

> 原文：<https://dev.to/petyosi/react-virtuoso-an-elegant-virtual-list-component-2ni9>

嘿大家好，

在与大型数据集进行了一番斗争后，我决定自己动手构建一个合适的组件，可以显示高度可变的项目，而不会有太多麻烦。请让我知道你对实现和特性集的想法。

一些有趣的细节:

*   该组件是用 typescript 编写的，并且使用了 [tsdx 脚手架](https://github.com/palmerhq/tsdx)。

*   内部状态[由 rxjs](https://github.com/petyosi/react-virtuoso/blob/master/src/virtuosostore.tsx) 实现。传播可观察到的变化与 hooks api 配合得很好。