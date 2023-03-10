# 使用 NativeScript 构建 Android 电视应用程序

> 原文：<https://dev.to/progress/building-an-android-tv-app-with-nativescript-4dbo>

如果你在这里，我猜 NativeScript 是你的第一选择(或者很快！)从一个代码库开发原生的跨平台应用，适用于 iOS 和 Android。我想标题泄露了一切，但是你知道你也可以为[安卓电视](https://www.android.com/tv/)开发本地脚本应用吗？

[![nativescript and android tv](img/0fe70ae4a74c454c4d62f3181760a388.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vn0q6EKK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/android-tv/nativescript-and-android-tv.png)

虽然 Android TV 还没有风靡全球，我们还没有正式支持这种集成，但它仍然是一个有趣的练习，可以作为你杀手级应用程序开发技能的另一个渠道。

**那么什么是安卓电视呢？**安卓电视是一个机顶盒(由安卓系统驱动),可以让你将内容传输到电视上📺。在很多方面，你可以将其与 Apple TV 进行比较，没有苹果生态系统固有的硬件限制。Android TV 上最常见的应用程序专注于媒体消费和游戏，但这个平台正在等待其下一个杀手级应用程序。

也许网络电视的时机已经成熟了！？😉

[![webtv](img/04956694a66bfc81e83038e629ed5c3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zJgasFR2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/android-tv/webtv.png)

## 一点历史

有一天，我们收到了一条来自一位 NativeScript 助手用户的消息，他想利用自己的 NativeScript 技能开发一款 Android 电视应用程序:

[![android tv request via intercom](img/9539dc78b713c335673a50a84616d665.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2PDT6LJV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/android-tv/intercom.png)

这有点💡对我们来说，当我们意识到这个平台应该在 NativeScript 总部有一个非官方的家。进入我们的老朋友“插件”Eddy Verbruggen。

Eddy 很快就完成了一个概念验证，有效地将 Android TV 作为 NativeScript 应用程序的另一个构建目标。点击此处观看简短视频。。

你知道他必须做什么吗？这非常简单。为了实现与 Android TV 的原始兼容性，您只需在您的`AndroidManifest.xml`文件中添加一行:

```
<category android:name="android.intent.category.LEANBACK_LAUNCHER" /> 
```

但仅仅是让一个应用在 Android TV 上构建和运行是一回事。拥抱平台的独特功能怎么样？

> 参考这个 GitHub 库获得一个基本的，但是功能齐全的，原生的 Android 电视应用程序的例子。

## D-Pad 控件

与允许你用手指控制 UI 的 iOS 或 Android 设备不同，使用 Android TV，你可以用一个小遥控器(D-pad 或[方向键](https://developer.android.com/training/tv/start/navigation.html))进行导航。因此，您确实需要具备以下能力:

*   使用 D-pad 控件导航用户界面，以及
*   能够*主动看到*你在 UI 中的位置。

[![android tv ui example](img/6e485616e1f78a14e5e3ec1bb3bb40ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iajmlACW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/android-tv/android-tv-ui.png)

这意味着对您的 NativeScript 应用程序进行一些小的 UI 调整。幸运的是，您有几个有效的选项:

### 选择器

你可以使用 [Android 选择器](https://developer.android.com/reference/java/nio/channels/Selector.html)为你的 UI 元素专门设置视图状态:

```
 <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
      <item android:state_pressed="true" android:drawable="@drawable/bluebutton_focused"/> <!-- pressed -->
      <item android:state_focused="true" android:drawable="@drawable/bluebutton_focused"/> <!-- focused -->
      <item android:state_hovered="true" android:drawable="@drawable/bluebutton_focused"/> <!-- hovered -->
      <item android:drawable="@drawable/bluebutton"/> <!-- default -->
    </selector> 
```

虽然这确实工作得很好，但它并不是真正的本地脚本方式。然而，它是可行的，而且给选择器布线也不太困难:

```
 export function elementLoaded(args: observable.EventData): void {
      const view = <ViewBase>args.object;

      // use a resource that specifies a 'focused' state:
      view.android.setBackgroundResource(identifier);
    } 
```

### 按键事件

可以说，实现“聚焦”状态的更合适的方式是使用 [Android 按键事件](https://developer.android.com/reference/android/view/KeyEvent.html) :

```
 public dispatchKeyEvent(event: android.view.KeyEvent): boolean {
        // you can respond to specific keycodes by fi. registering a listener and invoking it when appropriate
        console.log("D-Pad center button pressed? " + (event.getKeyCode() === android.view.KeyEvent.KEYCODE_DPAD_CENTER));

        // let's highlight the element that currently has the focus
        const tnsButton = <ViewBase>this.getCurrentFocus()["jsview"];
        if (tnsButton && tnsButton !== this.highlightedElement) {
          tnsButton.addPseudoClass("focused");
          if (this.highlightedElement) {
            this.highlightedElement.deletePseudoClass("focused");
          }
          this.highlightedElement = tnsButton;
        }
        return super.dispatchKeyEvent(event);
      } 
```

...您可以在您的 UI 中使用:

```
 export function elementLoaded(args: observable.EventData): void {
      const view = <ViewBase>args.object;

      // set a backreference so 'dispatchKeyEvent' in app.ts can swap CSS classes
      view.android["jsview"] = args.object;
    } 
```

...在你的 CSS 中什么是有效的`focused`伪类:

```
 .nav button {
      background-color: #87cefa;
    }

    .nav button:focused {
      background-color: #65aafa;
    } 
```

> 上面的例子是一个很好的机会来[构建一个新的 NativeScript 插件](https://docs.nativescript.org/plugins/building-plugins)，提示提示...

## 单独的 UI 视图

有了 NativeScript，您总是能够通过平台拥有[个独立的 UI 视图。通过一个巧妙的小技巧，你还可以拥有一个独立的 Android 电视视图:](https://developer.telerik.com/products/nativescript/platform-specific-development-nativescript/) 

```
 import * as utils from "tns-core-modules/utils/utils";

    if (utils.ad) {
      // Android: Load either the TV or phone UI
      const uiModeManager = utils.ad.getApplicationContext().getSystemService(android.content.Context.UI_MODE_SERVICE);
      if (uiModeManager.getCurrentModeType() === android.content.res.Configuration.UI_MODE_TYPE_TELEVISION) {
        console.log("Running on a TV");
        application.start({moduleName: "main-page-tv"});
      } else {
        console.log("Running on a Phone / Tablet");
        application.start({moduleName: "main-page"});
      }
    } else {
      // iOS
      application.start({moduleName: "main-page"});
    } 
```

> 记住，你可以在这个 GitHub 库中找到一个全功能的 NativeScript Android TV 应用。

## 包装完毕

现在轮到你了！尝试使用一个简单的 NativeScript 应用程序并添加这些定制。将应用程序部署到 Android TV 仿真器(作为 Android SDK 的一部分提供)进行测试。

最后，请在评论中告诉我们您的体验(或[提交问题](https://github.com/EddyVerbruggen/nativescript-android-tv/issues))，这样我们就可以继续为 Android TV 开发人员优化这一解决方案。