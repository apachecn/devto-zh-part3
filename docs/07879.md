# 安装 react-native-sentry 而不安装 react-native 链接

> 原文：<https://dev.to/acro5piano/install-react-native-sentry-without-react-native-link-38on>

【补充】
哨兵有完整的手册集成指南。你读这篇文章可能是浪费时间！

[https://docs.sentry.io/clients/react-native/manual-setup/](https://docs.sentry.io/clients/react-native/manual-setup/)T2【补充】

[再次添加]
现在我们可以将完全集成的 Sentry SDK 用于 React Native:

[https://github.com/getsentry/sentry-react-native](https://github.com/getsentry/sentry-react-native)T2【再次补充】

Sentry 是收集运行时错误的一个很好的工具。

react-native-sentry 是 sentry 官方包。虽然它仍处于测试阶段，但运行良好。

[https://github.com/getsentry/react-native-sentry](https://github.com/getsentry/react-native-sentry)

官方文件使用`react-native link`，但我不使用它，因为它经常导致错误。

[https://docs.sentry.io/clients/react-native/](https://docs.sentry.io/clients/react-native/)

```
yarn add react-native-sentry 
```

# iOS

```
open node_modules/react-native-sentry/ios/ 
```

然后，手动上药，将`RNSentry.xcodeproj`放入 Xcode 库。这种模式通常用于安装本机模块来反应本机应用程序。

在这里看我的照片:

[https://github . com/kmagiera/react-native-gesture-handler/issues/205 # issue comment-449900414](https://github.com/kmagiera/react-native-gesture-handler/issues/205#issuecomment-449900414)

然后，将`libRNSentry.a`加到`Build phases > Link Binary With Libraries`上。注意，不能添加会导致构建错误的`libRNSentryStatic.a`或`Sentry.framework`。

# 安卓

打开`android/settings.gradle`并添加以下行:

```
include ':react-native-sentry'
project(':react-native-sentry').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-sentry/android') 
```

然后，打开`android/app/build.gradle`，添加以下几行:

```
dependencies {
    compile project(':react-native-sentry')
    // other code here
} 
```

最后打开`android/app/src/main/java/com/nupp1/MainApplication.java`，添加包:

```
package com.your.app;

import android.app.Application;

import com.facebook.react.ReactApplication;
import com.facebook.react.ReactNativeHost;
import com.facebook.react.ReactPackage;
import com.facebook.react.shell.MainReactPackage;
import com.facebook.soloader.SoLoader;

import io.sentry.RNSentryPackage;  // <-- Add this line

import java.util.Arrays;
import java.util.List;

public class MainApplication extends Application implements ReactApplication {

  private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
    @Override
    public boolean getUseDeveloperSupport() {
      return BuildConfig.DEBUG;
    }

    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          new MainReactPackage(),
          new RNSentryPackage()        // <-- Add this line
      );
    }

    @Override
    protected String getJSMainModuleName() {
      return "index";
    }
  };

  @Override
  public ReactNativeHost getReactNativeHost() {
    return mReactNativeHost;
  }

  @Override
  public void onCreate() {
    super.onCreate();
    SoLoader.init(this, /* native exopackage */ false);
  }
} 
```

希望这对你有帮助。