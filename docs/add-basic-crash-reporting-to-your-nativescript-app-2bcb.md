# 向您的 NativeScript 应用程序添加基本的崩溃报告

> 原文：<https://dev.to/progress/add-basic-crash-reporting-to-your-nativescript-app-2bcb>

虽然我个人一生中从未写过一行错误的代码，但你们中的一些人很可能写过。开源的黄金法则是什么？

在您的用户有机会制造 GitHub 问题之前，识别并修复错误！

我们集体采取了许多方法来记录我们的应用程序的错误(无论是 web 还是移动的)。在我过去作为. NET web 开发人员的经历中，我经常采用一种基本的方法，利用全局`Application_Error`事件让我的应用程序发送一封简单的电子邮件，其中包含错误的详细信息。哦😐。它可以工作，但不能提供企业级应用程序所需的整合报告。

时代变了，出现了许多更强大的“崩溃报告”服务。一些更受欢迎的是 [CrashProbe](http://www.crashprobe.com/) 、 [Sentry.io](https://sentry.io/welcome/) 和 [Firebase Crashlytics](https://firebase.google.com/docs/crashlytics/) 。

[![sentry.io crashprobe crashlytics logos](img/75eb14727ef4e839186d2df54bb71d90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FzKe6GYA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/crashlytics/logos.png)

现在，如果你没有使用*任何*错误报告机制，我的本能是羞辱你，但实际上我想给你一个拥抱，让你知道一切都会好的。

今天，我们将学习如何利用 Crashlytics(Firebase 的一部分)来记录和报告 iOS 和 Android 的 NativeScript 应用程序中的错误。

> **提示:** NativeScript 5.2 带来了巨大的改进[错误记录改进](https://www.nativescript.org/blog/nativescript-5.2-comes-with-official-support-for-vue)！

## Firebase 插件

快速历史课:自 2011 年以来，Crashlytics 一直存在。几年后，它们被 Twitter 收购，最近又被 Google 收购，并包含在他们的 Firebase 产品中。

> 我并不是要让你完全市场化，但是如果你正在寻找一个有竞争力的、本地脚本优化的无服务器后端，看看 [Progress Kinvey](https://devcenter.kinvey.com/nativescript/guides/getting-started) 。

虽然 Firebase 包含许多服务，但我们今天唯一关心的是 Crashlytics。还有什么比通过 [NativeScript Firebase 插件](https://market.nativescript.org/plugins/nativescript-plugin-firebase)来利用这些服务更好的方式呢！

[![nativescript and firebase](img/81d5b238d28a6bc06161fe99f6ba2f35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7JySO9bH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/crashlytics/nativescript-firebase.png)

要开始使用 Firebase，你所需要做的就是前往 [Firebase 控制台](https://console.firebase.google.com/)并建立一个新的(免费)帐户。

从这里开始，我会让你按照[的简单说明](https://github.com/EddyVerbruggen/nativescript-plugin-firebase#prerequisites)来设置你的第一个 Firebase 项目，并正确配置你的 NativeScript 应用程序。

> **提示:**在插件安装期间，安装脚本会提示您激活一个或多个 Firebase 服务。在这一点上，确保在提示时用一个大的`Y`标记`Crashlytics`服务是很重要的。最坏的情况是，你可以通过进入`node_modules/nativescript-plugin-firebase`目录并运行`npm run config`来重新配置插件。

在你询问之前，请注意所提供的说明允许与 NativeScript Core(即普通 JavaScript)、Angular 或 Vue.js 一起使用！

## 使用碰撞分析工具

安装 Firebase 插件并在应用程序中初始化 Firebase 后，是时候更仔细地研究 Crashlytics 了。

> **注意:**此时，确保你的`firebase.nativescript.json`文件有`"crashlytics": true`和`"crash_reporting": false`标注。这将启用 Crashlytics 并禁用较旧的 Firebase 崩溃报告服务。如果你必须对这个文件进行修改，在你的应用中删除`platforms`文件夹(它会在下一次构建时返回！).

现在，在这一点上，你可以说，“我完成了！”...你不会错的。Crashlytics *将*开始跟踪应用程序崩溃，并在 Firebase 控制台中显示它们:

[![app crashes in firebase console](img/d7ce98f63650a22dfc2b9b7edb320102.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8RrDhqY2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/crashlytics/crashes.png)

但是我们可以做得更好！在应用崩溃之前捕捉错误*并向用户提供有意义的错误消息是一个更好的做法。有时我们不能预测容易出错的地方，但是当我们有可能改变的外部依赖时(比如远程 API)，我们应该谨慎行事，检查错误，并记录它们。*

## 捕捉错误

任何时候你想明确地记录一个错误，你只需要插入一些微小的代码片段。您首先需要导入 Firebase:

```
import * as firebase from 'nativescript-plugin-firebase'; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，调用 Firebase `sendCrashLog`方法将定制的错误日志发送到您的 Firebase 项目:

```
firebase.sendCrashLog({
  message: 'You screwed up! Here is the error: ' + error,
  showInConsole: true
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 您不仅可以编写一个自定义的`message`来登录 Firebase，还可以选择在您的控制台中显示相同的消息。

实践中一个很好的例子是`fetch`模块。由于`fetch`可以被配置为`catch`一个错误响应，我们可以很容易地记录任何错误:

```
fetch("https://httpbin.org/get")
    .then((response) => response.json())
    .then((r) => {
        // do something with the data!
    })
    .catch((error) => {
        // oh no!
        firebase.sendCrashLog({
          message: 'Error with that one API: ' + error,
          showInConsole: true
        });
    }
); 
```

Enter fullscreen mode Exit fullscreen mode

不过，情况越来越好了。由于`sendCrashLog`返回了一个承诺，我们也可以用一些更友好的 UI 提醒我们的用户注意一个问题(在这种情况下是一个本地提醒):

```
firebase.sendCrashLog({

  message: "Error!",

  showInConsole: true

}).then(

    () => {

      alert({

        title: "Sorry!",

        message: "Hey, we screwed up. Just thought you should know.",

        okButtonText: "OK"

      });

    }

); 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
作假错误

在你的应用程序中实现错误记录当然很好，但是我们怎么知道它在工作呢？幸运的是，在 iOS 和 Android 上都很容易假装崩溃:

```
import { isIOS, isAndroid } from 'tns-core-modules/platform';

declare const Crashlytics: any;

public force crash():void {
if(isIOS){
//一些保证抛出异常的无意义代码:)
var _ _ error = 1/0；
} else {
throw new Java . lang . exception(" Forced a exception . ");
 } 
 } 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
不速之客

随着一些崩溃的记录，是时候去你的便利的 Firebase 控制台检查你的报告了！

[![crashlytics nativescript example](img/b3271546f47d0e6aa7b22aec7a08f7d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DAga7Df2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/crashlytics/crashlytics-example.png)

**开心🐛挤压！**