# Android 和 iOS 推送通知，支持流聊天和原生反应

> 原文：<https://dev.to/lukesmetham/android-ios-push-notifications-with-stream-chat-and-react-native-3n0a>

# T2】

# 安卓& iOS 推送通知，带流聊天和 React 原生

[Stream Chat](https://getstream.io/chat) 允许您非常轻松地将推送通知添加到 React 原生应用程序中。在本教程中，我们将向您展示如何使用本地推送通知从头开始创建一个示例 React 本地应用程序，该应用程序可在 Android 和 iOS 设备上运行。

注意:*Expo 应用不支持本教程。我们需要访问本机代码来获取发送流聊天推送通知所需的设备令牌。如果您希望将它集成到现有的 Expo 应用程序中，您将需要弹出。*

如果你想看这个教程的代码，或者甚至克隆并跟随你自己，你可以在这个报告中找到它:【https://github.com/GetStream/react-native-chat-example-push】T2

首先，请确保您已经使用 Stream 创建了一个帐户，并在仪表板中创建了一个应用程序。

为了使事情变得更简单，我们将禁用 auth，以便我们可以轻松地测试推送消息。请注意，这仅适用于测试/开发环境。在开发模式下，您可以转到应用仪表板中的聊天选项卡，向下滚动至聊天事件，并启用禁用身份验证检查的切换。这意味着我们不需要发送有效的用户令牌——通常，我们需要从服务器生成有效的用户令牌)；您可以在这里找到关于使用 Stream 为生产应用程序生成有效令牌的更多细节。

[![](img/ade68e9ff8172b8a38697c77da509655.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--obt23Vre--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mimIWRC.png)

一旦你设置好了，确保你把你的 API 密匙和秘密放在一个安全的地方，以便在本教程的后面使用。

接下来，在您的终端中使用 react-native init chat-push-example 命令初始化您的新项目。您可以将聊天推送示例重命名为您喜欢的任何名称。

> 注意:如果您将它添加到现有的用 Expo 或 CRNA 创建的 React 本地项目中，您将需要弹出。请注意，弹出是不可逆的；但是，它需要访问原生 iOS 和 Android 功能。_ [弹出](https://github.com/react-community/create-react-native-app/blob/master/EJECTING.md)

我们将从安装我们需要的所有依赖项开始:

`$ yarn add stream-chat react-native-push-notifications react-native link react-native-push-notifications`

## iOS 设置

对于 iOS，我们将使用苹果的 APN 服务来支持推送功能，而不是 Expo 或 Firebase 云消息。

您还需要链接从 React Native 导出的 [PushNotificationIOS](https://facebook.github.io/react-native/docs/pushnotificationios) 。打开 Xcode，在左侧边栏中，确定您在“项目导航器”标签中(文件夹图标)。右键单击项目名称中的库目录，并选择`Add files to <Project_Name_Here>`。

[![](img/628be818411603e7b9a834c3c9985bde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--64CHukxV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Ql0eSjl.png)

将弹出一个文件对话框。导航到项目的根目录，然后导航到您的`node_modules`目录，并导航到如下所示的路径。单击添加。

`./node_modules/react-native/Libraries/PushNotificationsIOS/RCTPushNotification.xcodeproj`

[![](img/a9b1cbed5baf146132e3b2e773efbd9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SgTQsIIN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/JSc3LB2.png)

现在，在 navigator 侧栏中单击应用程序 Xcode 项目的根，并在中间窗格顶部的选项卡中单击 Build Phases。在下一个屏幕上，有五个下拉菜单。找到标有 Link Binaries with Libraries 的下拉列表，展开它，然后单击左下角的加号图标。

[![](img/60703960391954d606893fb1dc7c7a20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fg0E7cM6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/O2kKmry.png)

然后，聚焦搜索框，输入`RCTPushNotification`。您应该在列表中看到一个名为`libRCTPushNotification.a`的文件。选择它并单击添加。

[![](img/4291c54cf782660a64d665fa59241ace.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ln-NJZW9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/7faenYo.png)

最后，将下面的代码片段添加到您的`AppDelegate.m`文件的底部，可以在这里找到:

[![](img/39951b29ed4570d50d7698bf6f7cea3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XWL6N3aw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/YOzyzCP.png)T3】

```
// Required to register for notifications

 - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings

 {

  [RCTPushNotificationManager didRegisterUserNotificationSettings:notificationSettings];

 }

 // Required for the register event.

 - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken

 {

  [RCTPushNotificationManager didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];

 }

 // Required for the notification event. You must call the completion handler after handling the remote notification.

 - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo

fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler {

    [RCTPushNotificationManager didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];

 }

 // Required for the registrationError event.

 - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error

 {

  [RCTPushNotificationManager didFailToRegisterForRemoteNotificationsWithError:error];

 }

 // Required for the localNotification event.

 - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification

 {

  [RCTPushNotificationManager didReceiveLocalNotification:notification];

 } 
```

> 注意:这段代码将您的 iOS 应用程序挂接到 React Native 的 _[rctphusnotificationmanager](https://facebook.github.io/react-native/docs/pushnotificationios)，允许我们检索设备令牌并在 JavaScript 代码中处理推送通知。

## 在您的设备上运行

不幸的是，推送通知在 iOS 模拟器中不起作用，所以你需要一个物理 iOS 设备才能看到你的劳动成果。如果您想在设备上运行 React 本地应用程序，您必须编辑 Xcode 项目，以包含代码签名和推送功能。

打开 Xcode，从左边的导航栏中确保您的项目被选中。导航到“通用”标签，在“签名”下，确定您使用 Apple Developer 帐户登录。登录后，选择您的开发团队，并确保选中自动管理签名。

[![](img/5b2bb1e3687a19b4fc0da22f06a65bf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g4Rf2RfL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/RknSkmi.png)

在“General”选项卡的“Identity”下，确保您的捆绑包标识符与您用于配置带有 Stream 的推送通知的标识符相匹配。如果你还没有这样做，请前往[https://getstream.io/chat/docs/#push_ios](https://getstream.io/chat/docs/#push_ios)然后返回本教程。

最后，导航到“功能”选项卡，确保推送通知已启用:

[![](img/371612508cb66fbc0e94e297b10fde3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ECfRHLJ9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/89sZCHX.png)

你都准备好了！插入您的 iOS 设备，从 Xcode 的运行菜单中选择它，然后按下运行。

[![](img/d9fc7062f4b68c922543f4606d229d37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UcVl7ZH5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/N2Fj33h.png)

## 安卓设置

对于 Android，我们将使用 [Firebase 云消息](https://firebase.google.com/docs/cloud-messaging/)来支持推送通知。所以首先，你需要去 [Firebase 控制台](http://console.firebase.google.com/)，创建一个新的应用程序或者选择一个现有的项目。然后，转到项目设置和常规选项卡下。点击你的应用程序，添加一个 android 应用程序并下载你的 google-services.json 文件——你需要把它放在你的项目 Android 目录的根目录下。

现在，通过导航到您的项目级`build.gradle`文件(`._android_build.gradle`)，打开它，并将以下代码复制到您的依赖项中，确保`google-services.jsonfile`包含在您的 Android 项目的依赖项中，如下所示:

```
buildscript {

    // ...

    dependencies {

        // ...

        classpath 'com.google.gms:google-services:+'

        // ...  

    }

    // ...

} 
```

然后，在同一个目录中，找到`settings.gradle`文件，如果下面的文件不存在，就复制它。

> 注意:当您先前运行 react-native link 时，它应该已经添加了必要的文件；然而，最好总是仔细检查。联系有时是不稳定的。

```
include ':react-native-push-notification'

project(':react-native-push-notification').projectDir = file('../node_modules/react-native-push-notification/android') 
```

接下来，导航到`._android_app/src`并检查您是否有`res`目录。如果没有，创建它并在里面创建另一个名为 values 的目录。然后创建一个名为`colours.xml`的新文件，其内容如下:

```
<resources>
        <color name="white">#FFF</color>
</resources 
```

链接可能已经处理了下一步，但是再一次，在`android_app_src/main`中导航到项目 android 目录中的`MainApplication.java`。

并且检查它有这两个部分，用注释突出显示:

```
import com.dieam.reactnativepushnotification.ReactNativePushNotificationPackage;  // <--- THIS

public class MainApplication extends Application implements ReactApplication {

  private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
      @Override
      protected boolean getUseDeveloperSupport() {
        return BuildConfig.DEBUG;
      }

      @Override
      protected List<ReactPackage> getPackages() {

      return Arrays.<ReactPackage>asList(
          new MainReactPackage(),
          new ReactNativePushNotificationPackage() // <---- & THIS      
      );
    }
  };
  ....
} 
```

最后也是最重要的一点——转到`android_app_src_main_AndroidManifest.xml`并复制以下内容(下面的评论将有助于指导您进行特定的设置):

```
<!-- < Only if you're using GCM or localNotificationSchedule() > -->

  <uses-permission android:name="android.permission.WAKE_LOCK" />

  <permission
    android:name="${applicationId}.permission.C2D_MESSAGE"
    android:protectionLevel="signature" />
   <uses-permission android:name="${applicationId}.permission.C2D_MESSAGE" />

   <!-- < Only if you're using GCM or localNotificationSchedule() > -->
   <uses-permission android:name="android.permission.VIBRATE" />
   <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

<!-- Then, within the <application> block, place the following -->

    <receiver
    android:name="com.google.android.gms.gcm.GcmReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND" >
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <category android:name="${applicationId}" />
    </intent-filter>
  </receiver>

  <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
  <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
    <intent-filter>
      <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
  </receiver>

  <service android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationRegistrationService"/>

  <service android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerServiceGcm" android:exported="false" >
    <intent-filter>
      <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    </intent-filter>
  </service>

  <service
android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
        android:exported="false" >
    <intent-filter>
        <action android:name="com.google.firebase.MESSAGING_EVENT" />
    </intent-filter>
  </service> 
```

就是这样！我们准备开始编写一些普通的 React Native，并启动我们的流聊天客户端，准备发送推送通知。

现在是在您喜欢的平台上启动您的应用程序的好时机，如果您还没有这样做的话，请检查一切是否正常，没有任何可怕的红屏。

## 发送推送通知

多亏了[react-native-push-notifications](https://www.npmjs.com/package/react-native-push-notifications)，我们在每个平台上使用原生推送的同时，获得了平台无关的体验！因此，首先，在我们发送通知之前，我们将快速连接流客户端。

在项目的根目录下打开 App.js 文件。检查`componentDidMount`是否存在。如果文件不存在，向类中添加一个异步`componentDidMount`方法。

> 注意:这是您需要之前的流应用程序密钥的地方。如果您不在开发模式下，您还需要在这一步生成一个用户令牌。如果您正在生产，请参见此处的:
> 
> 如果您使用开发模式，请检查下面代码中的注释，取消对`changeAppSettings`调用的注释，然后您可以将任何字符串值作为令牌传递。

```
import React, { Component } from 'react';
import StreamChat from 'stream-chat';
import { API_KEY, USER_ID, USER_TOKEN } from 'react-native-dotenv';

class App extends Component {
  async componentDidMount() {
    const client = new StreamChat(API_KEY, null);
    await client.setUser({ id: USER_ID }, USER_TOKEN);

    PushNotification.configure({
      onRegister(token) {
        client
          .addDevice(token.token, token.os === 'ios' ? 'apn' : 'firebase')
          .then(() => {
            console.log(`registered device with token ${token}`);
          })
          .catch((e) => {
            console.error(`registering device failed: ${e}`);
          });
      },
      onNotification(notification) {
        notification.finish(PushNotificationIOS.FetchResult.NoData);
      },
      senderID: "1069091084846",
      requestPermissions: true
    });  
  }
  // ...
} 
```

出于本教程的考虑，我们创建了一个. env 文件来存储这些值，并使用如上所示的 [react-native-dotenv](https://www.npmjs.com/package/react-native-dotenv) 来获取它们——这在任何情况下对您的`API_KEY`来说都是一个好主意，但是您的`USER_ID`和`USER_TOKEN`可能来自真实世界设置中的其他地方，这取决于您的用例。

现在剩下的就是连接[react-native-push-notifications](https://github.com/zo0r/react-native-push-notification)。

在 componentDidMount 中的 client.setUser 调用下面，添加以下配置(并记住从“react-native-push-notification”导入 PushNotification 在顶部)。

下面分别是 iOS 和 Android 部分，但是，如果你的应用是跨平台的，你可以同时使用这两个部分:

```
PushNotification.configure({
  onRegister(token) {
    client
      .addDevice(token.token, token.os === 'ios' ? 'apn' : 'firebase')
      .then(() => {
        console.log(`registered device with token ${token}`);
      })
      .catch((e) => {
        console.error(`registering device failed: ${e}`);
      });
  },
  onNotification(notification) {
    // iOS only
    notification.finish(PushNotificationIOS.FetchResult.NoData);
  },
  senderID: "1069091084846", // (Android Only) Grab this from your Firebase Dashboard where you got google-services.json
  requestPermissions: true
}); 
```

这是让[react-native-push-notification](https://www.npmjs.com/package/react-native-push-notification)在您的应用程序中处理推送通知的最低配置。你应该看看他们的 [GitHub repo](https://github.com/zo0r/react-native-push-notification#readme) 以获得配置选项和设置的完整参考。

## 试试看

在本节中，如果您的构建面向 iOS，您需要插入一个真实的 iOS 设备——推送通知在模拟器中无法工作。然而，Android 模拟器*支持推送通知——只要确保你使用的模拟器安装了 Google Play 服务(在 AVD 管理器中显示为 Play Store 图标)*

[![](img/e62f1ab8c2b43527f1646945012b2bd5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WnE1LXB_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/aky0VIi.png)

如果您在启动应用程序时启用了远程 JS 调试，那么一旦您接受了允许通知的权限，您应该会看到带有令牌日志的注册设备出现在控制台中。

一旦你的设备被注册，确保你已经安装了 [getstream-cli](https://github.com/getstream/stream-cli) 并配置了你的 API 密匙和密码(以及其他必需的参数):

```
$ stream config:set 
```

配置完成后，您可以在终端中安全地运行以下命令:

```
$ stream chat:push:test 
```

首先，CLI 将提示您输入要向其发送通知的用户的 ID(您可以在您的。env–或之前存储它的任何地方)–然后将剩余的每个步骤留空，以便向注册到该用户的设备发送默认通知。有关推送 CLI 选项的详细信息，请参见此处的。

如果一切正常，你只是听到叮声或某种钟声，你的通知出现在你的设备上。恭喜你。😎