# Android:在你的应用中实现闪屏的简单方法

> 原文：<https://dev.to/artesanoandroid/android-simple-way-to-implement-a-splashscreen-in-your-app-3k0f>

# 首先，什么是闪屏？

闪屏是在移动设备上打开应用程序时出现的屏幕。所以，我们可以说这是用户的第一印象。它通常用于显示应用程序的徽标或与应用程序相关的图像。

在我个人看来，这是实现静态闪屏最简单的方法。

所以，我们来编码吧。

首先，用一个名为 SplashActivity 的空活动创建一个新的 android 项目。

完成后，我们就可以开始实施闪屏了:

## 1。在 res/values 文件夹下的 styles.xml 中创建一个自定义样式。

[![](img/35aa1646011d8dd9c1e9ec7bb28c2284.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2kJqkXBO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/HHWSVC9/Screen-Shot-2019-05-02-at-10-18-55.png) 

<figure>

<figcaption>图 1 -闪屏主题。</figcaption>

</figure>

## 2。在 AndroidManifest.xml 上，将我们之前创建的主题添加到 SplashActivity。

[![](img/49457aff2ca974eb70a15473418241cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_L3PxuEK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/SsJbNdh/Screen-Shot-2019-05-02-at-10-29-48.png) 

<figure>

<figcaption>图 2 -给 SplashActivity 添加主题。</figcaption>

</figure>

## 3。添加任何你想要的图像到 drawable 文件夹，我们将在闪屏中使用它。

[![](img/ec877b5778107852d0417c8b07e9146d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ybv0BXy_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/mDLsT8H/Screen-Shot-2019-05-02-at-10-34-20.png) 

<figure>

<figcaption>图 3 -可抽取文件夹。</figcaption>

</figure>

## 4。修改 activity_splash.xml。

[![](img/eabd6b3dd28bab860b4e9d2788b651d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NIhfbnCt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/XWXnvMc/Screen-Shot-2019-05-02-at-10-38-08.png) 

<figure>

<figcaption>图 4 - activity_splash.xml.</figcaption>

</figure>

## 5。添加新活动。我称之为主要活动。

[![](img/4f61fa70da9b860a08a9bde1e47f049a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SUGXBMQQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/VWcbtj4/Screen-Shot-2019-05-02-at-10-42-09.png) 

<figure>

<figcaption>图 4 - activity_splash.xml.</figcaption>

</figure>

## 6。现在，在 SplashActivity 中实现这段代码。我将使用一个处理程序来创建一个小的延迟，然后转到 MainActivity。

[![](img/61fa10a9b10306dfd8f2b66603e0bb6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iJPAY_IZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/KqKhxmc/Screen-Shot-2019-05-02-at-10-53-46.png) 

<figure>

<figcaption>图 5-splash activity . kt</figcaption>

</figure>

仅此而已！你的应用程序中有一个闪屏。当然，你可以使用一个动画，让它真的很棒，但我会在另一篇文章中展示它。

感谢阅读！

以防万一，以下是回购协议:

[回购](https://github.com/AlonsoAndroidDev/SplashActivitySample.git)