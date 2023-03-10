# 提高您的 Ionic 应用程序性能

> 原文：<https://dev.to/jsguru_io/improve-your-ionic-app-performance-2p2j>

[![](img/3f689bf293ac85b44fd2154c55fa34b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--udVJWmZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0041lc9rkx1igqj7cfst.png)

Ionic 允许开发人员使用 web 技术构建移动应用程序，并允许访问原生功能，如文件存储、相机、手电筒等。第一个版本于 2013 年发布，使用 Apache Cordova 和 AngularJS 构建，但最近的版本虽然仍使用 Apache Cordova，但使用 Angular 而不是 AngularJS。

Ionic 的最新版本，即版本 4，包括性能改进，但在我看来，它仍然很难与原生应用程序的性能竞争，甚至无法与原生应用程序竞争。

### **打造量产版**

这可能是一个明显的例子。在开发和测试时，我们通常会构建应用程序的调试版本。有多少次你构建了一个 apk，在安装后你意识到它是一个调试 apk？我已经做到了...有几次。

默认的构建命令将生成一个没有优化代码的调试 apk。

T2`ionic cordova build android --prod --release`

命令就可以了。它将生成一个具有精简和优化代码的生产就绪型 apk。

把这第一条建议看做是一个提醒..

### **添加一个服务人员**

给 Ionic 应用增加一个服务人员有它的好处。其中之一是它可以缓存您的静态资产，以便您的应用程序可以更快地加载。它不仅可以缓存你 app 的静态资产，还可以缓存 API 的一些数据？这可能有点用。

Ionic 内置了对服务人员的支持，添加非常简单。

如果你感兴趣，你可以在他们的文档中得到更多的信息。
[![](img/f04a095372df540b4c3cd43c15298479.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--_LcRGma2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://delivery.eusi.cloud/api/v1/f1a4305c-e431-4668-ae4c-02f78c656a41/media/s3/1549549262275_190206-18-2-Cache.png)

### **原生页面转场**

默认情况下，当切换到不同的页面时，Ionic 将使用 CSS 动画在视图之间切换。这在大多数情况下工作正常，但是你可能会注意到在低规格的设备上有时会有延迟，而且它也不是非常可定制的。

要解决这个问题，您应该切换到本机页面过渡，它使用本机硬件设备加速来激活过渡。

从我的经验来看，性能得到了提高，应用程序感觉更加原生。

它在 Android、iOS 和 Windows Mobile 上受支持，你可以很快开始使用它。

关于定制，你可以在 Android 和 iOS 中更改滑动方向、持续时间、延迟等等。

你可以在他们的[文档](https://ionicframework.com/docs/native/native-page-transitions/)中获得更多信息。

### **使用虚拟卷轴和无限卷轴**

如果你正在处理一个列表，你可能正在使用 Ionic list 组件，它完全可以提供基于平台的本地 UI。

然而，如果你有一个大的列表，你可能会注意到一些迟缓。为了摆脱这种情况，你可以开始使用虚拟滚动和无限滚动。

无限卷轴只是无限卷轴逻辑的一个离子组件。当用户到达列表末尾时，它将调用某个动作。它非常适合分页，因为您可以加载一定数量的项目，并且在需要时加载更多的项目并将其追加到原始列表中。它还提供了对加载指示器的定制，您可以在其中更改文本和微调器。

虚拟滚动将获取数组的完整列表，但只呈现当前可见的项目，这可以极大地提高性能。当用户滚动时，视图会被重用，但根据行的不同，视图中会填充不同的数据。

什么时候应该使用虚拟卷轴？

当您有更多的数据，但不需要无限滚动选项时(例如，所有数据都在一个 API 调用中加载)，您可以使用它，它将使您受益匪浅！

### **结论**

无论您使用哪种入门套件，混合应用程序都不会默认提供出色的性能。在尝试任何性能优化技巧之前，不要忘记优化您自己的代码，删除未使用的导入和 CSS，并优化您的图像！即使是很小的事情也会有所不同。

### 出发前…

如果你喜欢读这篇文章，请**分享**它。你应该看看我们的其他出版物，你可能也会喜欢它们！我们不时地写一些关于软件开发、技巧和诀窍，以及如何成为一个更好的开发人员和商业人士的文章。加入我们不断改进的旅程吧！

在[脸书](https://www.facebook.com/jsguruio/)、[推特](https://twitter.com/jsguru_software)、 [LinkedIn](https://www.linkedin.com/company/jsguru) 、 [Medium](https://medium.com/jsguru) 或 [DEV.to](https://dev.to/jsguru_io) 上关注我们。

*原载于*[*jsguru . io*](https://jsguru.io/blog/improve-your-ionic-app-performance)*。*