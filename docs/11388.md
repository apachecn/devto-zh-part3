# 使用 Lottie 的 Android 动画

> 原文：<https://dev.to/pabiforbes/android-animations-using-lottie-34df>

# 动画，各有不同

我已经写了两篇博文，都是基于我第一次谈论 Android 动画。我一点也不知道，两个月后，我将被赋予一项任务，要求我重温动画。但是这一次，我不得不用不同的方式来制作动画。*如何？！*你可能会问...

# 利用洛蒂

任务是用团队中插画师的自定义加载器替换 Android 应用程序中的所有进度条。所以这一次，我不会使用*视图动画框架*和*属性动画框架*。
插图画家以 json 文件的形式提供了定制的加载器动画。那么我们如何加载 json 动画文件呢？通过使用一个叫做洛蒂的酷图书馆。

Android 版 Lottie 是由 [Airbnb](https://airbnb.design/introducing-lottie/) 创建的一个库，帮助渲染使用 After Effects 创建的动画。

# 设置

在项目中的 app gradle 文件内添加以下依赖项:

`dependencies {
implementation 'com.airbnb.android:lottie:$lottieVersion'
}`

这就是你开始工作所需要的一切。

# 加载动画

要使用 Lottie 工具，动画需要放在*资产*(...\app\src\main\assets)文件夹。这是洛蒂加载动画的地方。
动画放入文件夹后，我们可以将它们添加到布局中。

### 向布局添加洛蒂动画视图

洛蒂动画视图用于加载洛蒂动画。为了将 LottieAnimationView 添加到布局中，我们使用了如下的`<com.airbnb.lottie.LottieAnimationView/>`标签:

`<com.airbnb.lottie.LottieAnimationView
android:layout_width="match_parent"
android:layout_height="wrap_content"
app:lottie_autoPlay="true"
app:lottie_loop="true"
app:lottie_fileName="your_filename.json" />`

例如，假设我们有一个指纹动画，希望在验证用户指纹时显示。我们会将其添加到布局中，如下所示:

`<com.airbnb.lottie.LottieAnimationView
android:layout_width="wrap_content"
android:layout_height="wrap_content"
app:lottie_autoPlay="true"
app:lottie_loop="true"
app:lottie_fileName="fingerprint.json" />`

#### 结果:

[指纹动画](https://thepracticaldev.s3.amazonaws.com/i/a2pwf45j6sasipq7yn8v.gif)

动画有助于使您的应用程序栩栩如生，有助于为您的用户提供视觉反馈，并使用户界面更具吸引力和可用性。洛蒂是一个了不起的工具，可以帮助实现这一目标。去试一试吧。

# 以前的博客帖子

*   [使用动画让您的 Android 应用变得栩栩如生~第 1 部分](https://dev.to/pabiforbes/devfestza-bringing-your-android-application-to-life-using-animations-part-1-173p)
*   [使用动画让您的 Android 应用变得栩栩如生~第 2 部分](https://dev.to/pabiforbes/devfestza-bringing-your-android-application-to-life-using-animations-part-2-3291)

# 快速教程

[Github](https://github.com/PabiMoloi/AnimationsWithLottie/tree/develop)

# 酷资源:

*   [洛蒂档案](https://lottiefiles.com/)
*   [支离破碎的播客](https://dev.to/fragmented/082-airbnbs-gabriel-tells-how-to-animate-with-lottie)
*   [洛蒂~开始吧](https://airbnb.io/lottie/android/android.html#getting-started)

# 学分:

*   [指纹动画文件](https://lottiefiles.com/user/7852)