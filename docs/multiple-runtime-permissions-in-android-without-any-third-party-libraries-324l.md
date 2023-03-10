# Android 中的多个运行时权限，没有任何第三方库

> 原文：<https://dev.to/wajahatkarim/multiple-runtime-permissions-in-android-without-any-third-party-libraries-324l>

#### 在本分步指南中了解如何添加多个运行时权限

> 这篇文章是我今天学习的系列的[的一部分，最初发布在我的](https://medium.com/p/65c64e1dcb6) [TIL Github 库](https://github.com/wajahatkarim3/Today-I-Learned/)和我在 wajahatkarim.com[的网站](http://wajahatkarim.com)

在我目前工作的项目中，我有一项任务是在一个 android 应用程序中添加运行时权限，该应用程序的代码非常旧，并且使用传统的方法和框架/工具。通常，我在我的所有应用程序中使用 [Ted 权限](https://github.com/ParkSangGwon/TedPermission)作为运行时权限，我必须说这是我见过的一个令人惊叹的库，考虑到 Android *中复杂的场景和运行时权限流(感谢谷歌总是确保让每件事情比以往更复杂)*，这个库让运行时权限变得轻而易举。干得好[泰德公园](https://medium.com/u/ecac7eac58cd)。

[parksangwon/ted permission](https://github.com/ParkSangGwon/TedPermission)

Android 在 API Level 23(Android Marshmallow 6.0)或更高版本中引入了[运行时权限](https://developer.android.com/guide/topics/permissions/overview)。权限有一个非常复杂的流程来实现，如下图所示:

<figure>[![](img/adeaca45526dc6624f35c22ab5fcba40.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KyZhx2mW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/919/0%2ALuQugCqLUU26Vwp-.png) 

<figcaption>图片来源: [Ted 权限](https://github.com/ParkSangGwon/TedPermission)</figcaption>

</figure>

今天，当我正在寻找一种非常简单的方法来实现这一点时，我震惊地看到 Google 是如何将这一简单的事情变得如此复杂和令人困惑，就像他们对相机 API 所做的那样。仅仅是在代码的开头添加两个简单的运行时权限，就花了我一整天的时间。我是这样做的。

> 重要提示:您不应该一次请求所有权限。相反，你应该只在应该使用它的时候请求相关的许可。我要求在开始的所有权限，因为我们的代码库是非常古老和复杂的。

我的应用程序需要*存储*和*位置*权限。因此，在 Android 清单文件中添加这些权限，如下所示:

[![](img/0471e713b032b023b469d0a0127c4735.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t8Nr1iI1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACr30LVf1II7JC0I5TPD3Iw.png)

现在，应用程序一启动，就会要求两个(位置和存储)权限。如果两种权限都被授予，那么应用程序将继续正常运行。我们可以在第一个活动的 onCreate()方法中这样做，如下所示:

[![](img/feb7eb367dee29b5a9d3cb8835888d9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xPz1wZym--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVAGMcWntbgrR85F8Olh-nw.png)

请注意，在上面的代码中，我创建了一个 String[]数组，其中包含了我们将在此请求的所有权限。现在，让我们实现 checkAndRequestPermissions()方法。

[![](img/849b773fcadaa9431a3daf89b0bbf337.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CVkDpf8z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZrHn5aQ8hLx4nyp8_YGN6Q.png)

正如我们在上面的代码中看到的，我们可以使用 context compat . checkselpermission()方法检查任何权限的状态。而且我们可以用 activity compat . request permissions()方法请求任何权限。这些方法已经包含在支持库中。

现在，如果 listPermissionsNeeded 不为空，这意味着至少有一个或多个权限被拒绝。因此，requestPermissions()方法将显示权限对话框，并将调用 onRequestPermissionsResult()回调方法

[![](img/0de967db32257ed8eac36056581ba347.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ve8d8Q26--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/276/0%2AeP-yGS-wucHZ9F3A.png)

现在，这是整个案件在请求许可后的情况。第一次，android 会正常显示权限。如果全部被接受，那么一切正常。如果任何一个或全部被拒绝，那么它显示解释对话框(由开发者创建)来告诉用户为什么许可是重要的。该对话框将允许用户再次授予权限。现在，android 将再次显示许可对话框，但这一次带有一个额外的复选框“不再询问”。

[![](img/2bed78b73ac975fe4c636fa398da1d31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oATnUcY6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/276/0%2AkjeyFWB2bErkbtVA.png)

如果任何权限已被“不再询问”复选框拒绝，则该权限只能通过应用程序的**设置**获得。由于我的应用程序没有这些权限无法工作，所以我将显示另一个对话框，允许用户前往应用程序的**设置**屏幕，并允许手动授予权限。

<figure>[![](img/ce9ec4094830d7b8fff8632c4b9704d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--knbxD2cb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/320/0%2AfJa0II29-vyh8mAC.png)

<figcaption>Android 上 Chrome 的权限屏幕</figcaption>

</figure>

我们可以使用另一个方法 activity compat . shouldshowrequestpermissionration(context，permission)来实现这一点，该方法保留“不再询问”检查的记录。如果这个方法返回 true，这意味着 Android 将显示请求权限对话框。如果为假，Android 将不会显示对话框，因为用户已经选择了“不再询问”。因此，您需要将用户重定向到设置->权限屏幕，以允许用户手动授予权限。

现在，让我们看看 onRequestPermissionsResult()方法中的代码。我也添加了一些注释，所以这应该是不言自明的。

[![](img/2ba46d090f75b6facef05c042eba22bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yFn0MugK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmQ0JXufFma_b6Q7Br5GXZg.png)

下面是 showDialog()方法。这只是为了创建 AlertDialog。

[![](img/922ef022392224899b22b07b53799572.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LMIE6XSr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABIs-o_7sy3xmShSfJ50vCA.png)

我希望这有助于您的应用程序开发，并解决运行时权限的问题。

**Wajahat Karim** 毕业于 NUST，是一名经验丰富的移动开发者，一名活跃的开源贡献者，也是两本书[学习 Android 意图](https://www.amazon.com/Learning-Android-Intents-Muhammad-Usama/dp/1783289635)和[用 Unity 掌握 Android 游戏开发](https://www.amazon.com/Mastering-Android-Game-Development-Unity/dp/1783550775/)的合著者。在业余时间，他喜欢花时间和家人在一起，做编码实验，喜欢写很多东西(主要是在博客和媒体上)，并且是开源的热情贡献者。2018 年 6 月，他的一个库在 [Github 趋势](https://github.com/trending)上成为第一名。他的库在 Github 上有大约 2000 颗星星，并被全球各地的开发者用于各种应用程序中。在 Twitter 和 [Medium](https://medium.com/@wajahatkarim3) 上关注他，获得更多关于他的写作、Android 和开源工作的更新。

[Wajahat Karim (@WajahatKarim) | Twitter](https://twitter.com/WajahatKarim)

此外，如果你有任何想让他回答的问题，可以通过他的网站联系他，地址是[wajahatkarim.com](http://wajahatkarim.com)，主题栏是亲爱的瓦贾哈特。

* * *