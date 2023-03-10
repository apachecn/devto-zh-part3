# SDP——可扩展的大小单位...去救援😁

> 原文：<https://dev.to/sduduzog/sdp---a-scalable-size-unit-to-the-rescue--4nh3>

*我在 dev.to 上的第一篇 2019 帖子新年快乐，你真棒*😎

我在这里写了一篇关于去年我的副业项目的文章，副作用非常惊人🙌下载量增加了很多，我也得到了好评。

[![sduduzog](img/aca6e88e812d318765e50104fa969309.png)](/sduduzog) [## 辅助项目成功🎉

### beautys s gumede

#showdev #android #kotlin](/sduduzog/side-project-success--2i3k)

从 google play 和电子邮件上收到的应用反馈来看，有一个问题最为突出。

这也用作封面图像😐
[![google play review screenshot](img/464d4e9ccf3f79c5a4cea1d989b0cbf5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--cH9Pb0wk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q888pkulz9n2fidrsnro.png)

另一篇评论🤨
[![another google play screenshot](img/f8769516287200c6d3936a3f5aa04024.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--n8Ok4pOT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ui5ycq7yi5q2jaqd0iiy.png)

最后一张截图，我发誓😑
[![screenshot of an email review](img/830bff8d089bc91458c61b4edbee256c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--YW0TVylO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yynb3fyt8f4q0bcecwyq.png)

*我给图像上色，以区别于现场背景*

基本上越来越多的人注意到了我认为“不是问题”的“小事”**不同屏幕尺寸上的文本尺寸**

我真的不记得我收到了多少关于小屏幕设备上文本大小问题的请求，但这让我变得偏执。

看，这个应用程序是一个极简的启动器，允许你选择 5 个应用程序出现在你的主屏幕上，并且只显示一个时钟和你的选择。**问题是**在较小的屏幕上，应用程序文本会与时钟重叠😫

我试图针对不同的密度创建不同的资源，但那是一场灾难。我知道我永远不会顺利地覆盖每一个用例。

**直到我遇到了**

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)T1】intuit/[SDP](https://github.com/intuit/sdp)

### 一个 Android SDK，提供了一个新的大小单位——Sdp(可伸缩 DP)。该尺寸单位随屏幕尺寸而缩放。

<article class="markdown-body entry-content container-lg" itemprop="text">

# SDP——可扩展的大小单位

一个 android lib，提供了一个新的大小单位——Sdp(可伸缩 DP)。该尺寸单位随屏幕尺寸而缩放。它可以帮助 Android 开发者支持多屏。

关于文本视图，请参考 [ssp](https://github.com/intuit/ssp) ，它基于文本的 sp 尺寸单位。

# 注意力

小心使用！例如，在大多数情况下，你仍然需要为平板电脑设计不同的布局。

# 例子

[这里的](https://github.com/intuit/sdp/blob/master/sdp-android/src/main/res/layout/sdp_example.xml)是使用 sdp 构建的单一布局:

[![sdp example](img/a29281880bf88c9bbdcee3e57d29018a.png)](https://github.com/intuit/sdp/blob/master/sdp_example.png)

这里的[是使用 dp 构建的相同布局:](https://github.com/intuit/sdp/blob/master/sdp-android/src/main/res/layout/dp_example.xml)

[![dp example](img/1ddea3a7a5613cbe24c733114d4ce8f1.png)](https://github.com/intuit/sdp/blob/master/dp_example.png)

您可以看到，sdp 随屏幕尺寸而缩放，而 dp 在所有屏幕尺寸下保持不变。

# 入门指南

将 sdp 添加到您的项目中(使用 Android Studio 和 Gradle):

将实现“com . intuit . SDP:SDP-Android:1 . 0 . 6”添加到 build.gradle dependencies 块中。

例如:

```
dependencies {
  implementation 'com.intuit.sdp:sdp-android:1.0.6'
} 
```

请参见 [sdp_example.xml](https://github.com/intuit/sdp/blob/master/sdp-android/src/main/res/layout/sdp_example.xml) 了解如何使用…

</article>

[View on GitHub](https://github.com/intuit/sdp)

## 和

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)T1】intuit/[ssp](https://github.com/intuit/ssp)

### 基于 sp 尺寸单位的 sdp 项目变体。

<article class="markdown-body entry-content container-lg" itemprop="text">

# SSP——文本的可伸缩尺寸单位

一个 android lib，提供了一个新的大小单位——ssp(可伸缩 sp)。该尺寸单位根据 sp 尺寸单位(用于文本)随屏幕尺寸缩放。它可以帮助 Android 开发者支持多屏。

这是应该用于非文本视图的 [sdp](https://github.com/intuit/sdp) 大小单位的同级。

# 注意力

小心使用！例如，在大多数情况下，你仍然需要为平板电脑设计不同的布局。

# 例子

[这里的](https://github.com/intuit/ssp/blob/master/ssp-android/src/main/res/layout/ssp_example.xml)是使用 ssp 构建的单一布局:

[![ssp example](img/d5abffcbc07e4550769a50a2595ea063.png)](https://github.com/intuit/ssp/blob/master/ssp_example.png)

这里的[和](https://github.com/intuit/ssp/blob/master/ssp-android/src/main/res/layout/sp_example.xml)是使用 sp:

[![sp example](img/751756323161b425847e73adf8a1d5cc.png)](https://github.com/intuit/ssp/blob/master/sp_example.png)

您可以看到，ssp 随着屏幕大小而扩展，而 sp 在所有屏幕大小上都保持不变。

# 入门指南

要将 ssp 添加到您的 Android Studio 项目中:

将实现“com . intuit . ssp:ssp-Android:1 . 0 . 6”添加到 build.gradle dependencies 块中。

例如:

```
dependencies {
  implementation 'com.intuit.ssp:ssp-android:1.0.6'
} 
```

参见 [ssp_example.xml](https://github.com/intuit/ssp/blob/master/ssp-android/src/main/res/layout/ssp_example.xml) …

</article>

[View on GitHub](https://github.com/intuit/ssp)

我相信这些库能够为下载我的应用的数百名用户提供一致的用户界面。

我真的只设计了一种布局，可能用`@dimen/_64sdp`来表示页边空白或文本大小，用`@dimen/_12ssp`来表示`android:textSize`，我希望在许多屏幕尺寸上保持一致。

它支持最低 sdk 版本 16，我也将测试平板电脑。

**注意**不要忘记*所有屏幕尺寸*的方法并不是大多数 ui 设计解决方案的解决方案。

快乐编码