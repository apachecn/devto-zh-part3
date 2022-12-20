# React Native 获取远程图像尺寸 Android IOS

> 原文：<https://dev.to/skptricks/react-native-get-remote-image-dimensions-android-ios-pll>

来源: [React Native 获取远程图片尺寸 Android IOS](https://www.skptricks.com/2019/04/react-native-get-remote-image-dimensions.html)

本教程解释了如何在 react 本地应用程序中获取远程图像的高度和宽度尺寸。React native Image 组件提供了一个名为 getSize 的方法，用于获取远程图像的宽度和高度尺寸。

React Native 中图像组件的 getSize()函数:
在显示图像之前，获取图像的宽度和高度(以像素为单位)。如果找不到图像或下载失败，此方法可能会失败。为了检索图像尺寸，可能首先需要加载或下载图像，之后图像将被缓存。这意味着，原则上您可以使用此方法预加载图像，但是它不是为此目的而优化的，并且将来可能会以不完全加载/下载图像数据的方式实现。一个合适的、支持的预加载图像的方法将作为一个单独的 API 提供。

[![](img/08e757f6fe0e6354c08a0c70d7503e05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JeX_TETk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-HmMhNiJ9kEk/XMf2YM-_HtI/AAAAAAAACvg/vgKikxM4HP0vTu1rQClMiWucuOqGHe6YwCLcBGAs/s400/image.jpg)

点击此处阅读更多内容...