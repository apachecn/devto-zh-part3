# 动荡的一个月:配置 Firebase Auth 以便在 Android 上登录 Google

> 原文：<https://dev.to/abraham/a-month-of-flutter-configure-firebase-auth-for-sign-in-with-google-on-android-4chp>

*最初发表于[bendyworks.com](https://bendyworks.com/blog/a-month-of-flutter-configure-sign-in-with-google-android)。*

昨天我加了[签到按钮](https://bendyworks.com/blog/a-month-of-flutter-fabulous-authentication)。现在让我们让它做点什么。

首先，我将创建一个新的 [Firebase 项目](https://firebase.google.com/)，它将在开发中使用。一旦我准备好发布应用程序，我将创建一个生产项目。

[![creating Firebase development project](img/58a0732ec9f87dfbc10893bad9621954.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RdI2mU5b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pfex1le9i6fhtrab3uom.png)

项目创建完成后，我将启用 Google 作为 Firebase Auth 的登录提供者。启用 Google 需要一个应用名称和一个电子邮件地址。电子邮件地址可以是 Google Group 邮件列表。我确保这个群被配置成任何人都可以发帖，但是没有人可以加入，只有成员可以阅读。

[![enable Google auth](img/bea771b073573901b04b045c9da5c244.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--271UZWx6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iofxnlg1acqrm7e4o6do.png)

如果 Twitter 或电子邮件/密码更适合该应用的目标用户，那么谷歌有很多替代品。

[![list of Firebase Auth providers](img/ff1a29e40458bef20ddfdf4c23481864.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9q7jj-l2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ixry1ayhti0tqn5e236.png)

在`android/app/build.gradle`内，我将用`app.birb`替换默认的`com.example.<name>` `applicationId`。一个[应用程序 ID](https://developer.android.com/studio/build/application-id.html) 应该是你拥有的一个域，其中的各个部分顺序相反。

登录 Google 需要认证 [API 客户端](https://developers.google.com/android/guides/client-auth)。这基本上意味着在访问一些 Google APIs 时必须使用证书。由于 Birb 应用程序目前正在使用开发 Firebase 项目，我现在将使用 Android Studio [调试证书](https://developer.android.com/studio/publish/app-signing#debug-mode)并在将来生成生产证书。

为了获得调试证书指纹，使用了`keytool`程序。这是随 Android Studio 一起安装的，但可能不在您的系统路径中。我使用 [`flutter doctor -v`](https://flutter.io/docs/deployment/android#create-a-keystore) 来查找 Java 垃圾箱的位置。

```
$ /usr/local/android-studio/jre/bin/keytool -list -v -alias androiddebugkey -keystore ~/.android/debug.keystore
Enter keystore password:  
Alias name: androiddebugkey
Creation date: Oct 22, 2018
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: C=US, O=Android, CN=Android Debug
Issuer: C=US, O=Android, CN=Android Debug
Serial number: 1
Valid from: Mon Oct 22 18:57:30 CDT 2018 until: Wed Oct 14 18:57:30 CDT 2048
Certificate fingerprints:
     MD5:  32:B6:A3:75:EA:76:C7:AB:4A:A5:D4:48:AD:F3:09:52
     SHA1: 0C:DC:5F:08:F0:45:EF:F0:7B:5C:CA:F2:76:52:CC:EA:16:3F:94:08
     SHA256: 36:33:30:07:40:12:A4:30:2A:DD:9F:8C:FD:BE:92:62:F2:FB:C3:37:09:6F:DB:62:C9:60:7C:5E:8A:F8:21:59
     Signature algorithm name: SHA1withRSA
     Version: 1
~~~{% endraw %}

With the SHA-1 fingerprint, {% raw %}`0C:DC:5F:08:F0:45:EF:F0:7B:5C:CA:F2:76:52:CC:EA:16:3F:94:08`{% endraw %}, at the ready, it's back to Firebase. I'll add an Android app to the project I created earlier. In the future I will also create an iOS app.

![adding Android app to Firebase project](https://thepracticaldev.s3.amazonaws.com/i/du1u43p03h20zu6kbbfg.png)

With the package name and fingerprint configured, Firebase will now prompt to download a {% raw %}`google-services.json`{% endraw %} file. This contains various Google Cloud and Firebase service details and should be put in the {% raw %}`android/app`{% endraw %} directory. There isn't any sensitive information in the file but for an open source project it will need to be different for every developer so I'm adding it to {% raw %}`.gitignore`{% endraw %}.

![adding Android app to Firebase project](https://thepracticaldev.s3.amazonaws.com/i/gghubkwy8ta1bdyuxk29.png)

Now I'll add a couple of dependencies to the Android app and to Flutter.

![adding Android app to Firebase project](https://thepracticaldev.s3.amazonaws.com/i/1ma7iopjpmjkhy1geibw.png)

{% raw %}`com.google.gms:google-services` gets added to `android/build.gradle`.

~~~gradle
buildscript {
    dependencies {
        // Add this line
        classpath 'com.google.gms:google-services:4.2.0'
    }
}
~~~

In `android/app/build.gradle` there are two additions.

~~~gradle
dependencies {
  // Add this line
  implementation 'com.google.firebase:firebase-core:16.0.6'
}

// Add to the bottom of the file
apply plugin: 'com.google.gms.google-services'
~~~

In `pubspec.yaml` require the [`google_sign_in` package](https://pub.dartlang.org/packages/google_sign_in) and the [`firebase_auth` package](https://pub.dartlang.org/packages/firebase_auth).

![adding Android app to Firebase project](https://thepracticaldev.s3.amazonaws.com/i/6olvkhed1qactarm0k2e.png)

Well the Sign in with Google button still doesn't do anything but maybe tomorrow it will.

## Code changes

- [#43 Configure Firebase Auth](https://github.com/abraham/birb/pull/43) 
```

Enter fullscreen mode Exit fullscreen mode