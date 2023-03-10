# 如何修复 Cydia Impactor 168 错误(provision.cpp:168)

> 原文：<https://dev.to/bobkyle101/how-to-fix-cydia-impactor-168-error-provision-cpp-168-omd>

你很有可能知道 Cydia 撞击器工具。这是每个 iOS 设备持有者的必备工具。它提供了用户友好的界面来安装所有这些应用程序以外的应用程序商店。

然而，我在使用 Cydia Impactor 安装 YouTube++ IPA 文件时，它显示:provision.cpp:168。我尝试重新安装，但抛出同样的错误。冲浪了一下后，这里是我带来的 cydia impactor 168 的解决方案。其中一个列出的解决方案肯定会工作。

[![cydia impactor 168](img/c796c07fd9296829106c10ec69593e12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yx5O9euh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mWx9yUo.jpg)

### **1。创建特定于应用程序的密码**

如果你启用了 Google 2fa，这很有帮助。大多数 iOS 设备都启用了 2fa。在这种情况下，您需要创建一次性的特定于应用程序的密码。

1.  使用您的 Apple 凭证登录 Apple web entrance。
2.  转到安全，并点击应用程序特定的密码。
3.  通过命名创建一个标签，并点击生成密码。
4.  现在，在 cydia impactor 上拖放您的 IPA。
5.  输入我们在苹果入口创建的苹果 ID 和密码。

### **2。吊销证书**

这是它诞生后我尝试的第一个方法。下面是解决它的指南:

1.  开放式 Cydia 冲击器
2.  将 iOS 设备连接到电脑。等待它检测您的设备。
3.  前往 Xcode >撤销证书。

就是这样！现在尝试重新安装你的 IPA。

等待它安装完成！

### **3。使用安装包**手动安装

如果列出的两种解决方案都不起作用，手动安装应该可以。

1.  开放式 Cydia 冲击器
2.  将 iOS 设备连接到电脑。等待它检测您的设备。
3.  转到设备>安装包
4.  从你的电脑中找到你的 IPA 文件，它将开始安装。

在你完成的地方，你最终会获得成功。