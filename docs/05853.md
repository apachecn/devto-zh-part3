# app ium 1 . 9 . 0–主要特性和挑战

> 原文：<https://dev.to/pcloudy/appium-1-9-0-key-features-and-challenge-465f>

[![](img/8ac33152072ea19d1655fcb619f57c10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z10xMKHq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r4pbixnvozazhdipchcr.png)

随着 Appium 1.9.0 的发布，我们发现它是一个包含多个更新的特性版本。此版本更新中提出的一些关键功能包括:

a.Appium 用户一直面临老版本 Appium 解锁设备的问题。这一直是一个令人头疼的问题，但是 Appium 已经通过 1.9.0 的更新解决了这个问题。即使设备保持锁定，Appium 1.9.0 也非常有能力解锁设备并顺利运行您的脚本。

b.如果脚本中的任何地方都没有标识该对象，您将能够在 Appium 日志本身中找到错误。

c.Appium 1.9.0 的执行速度比旧版本快得多。

d.日志信息比以前更加深入和详细。

下面让我们来看看功能发布更新:
(来源:【https://github.com/appium/appium/releases】T2

一般

完全支持 W3C 规范。
为 Android automation 添加完整测试版的 [Espresso 驱动程序](https://github.com/appium/appium-espresso-driver)
(通过将 automationName 功能指定为
“Espresso”来使用)。
为三星 Tizen 设备添加驱动程序
添加图像查找元素策略、
修复——异步跟踪服务器参数，并重命名为——长堆栈跟踪
示例代码已移至主存储库，以帮助维护。
修复状态检索以加快性能。
iOS

增加对 Xcode 10 beta 5 和 iOS 12 beta 5 的支持。
增加对 MacOS Mojave 测试版的初步支持。
增加 face id 生物识别支持。
修复设备时间检索，增加可选格式参数。
如果结束会话时没有 idevicesyslog，则不会崩溃。
在 Safari 中页面变化时处理框架。
添加所需的功能:
remoteDebugProxy——端口或 Unix 域套接字，其上有一个用于
远程调试器的代理。
safariGarbageCollect–在 Safari 中打开/关闭 JS 垃圾收集。
show safarinetworklog–打印 Appium 服务器日志中的 Safari 网络日志。
mjpegServerPort–屏幕截图可以传输到的端口。
修复设置更新的处理，因此模拟器不会不必要地重新启动。
允许从真实设备中拉出文件夹。
添加 mobile: getContexts 执行函数，以检索元信息(标题、
url 等)。)关于可用上下文。
修复证书检索和处理。
修复 cookie 处理，以允许安全 cookie。
修复 Safari 超时问题。
增加支持检索 Safari 网络日志，为 Safari 网络日志类型。
安卓

将 Chromedriver 更新至 v2.41\.
在 Android 7+中获取用于 Webviews 的 Chrome 版本，以找到正确的 Chromedriver。
确保在测试过程中清理 UiAutomator 进程。
修复 autoWebview 功能的处理。
新的期望功能:
mjpegScreenshotUrl–屏幕截图的 Url。
chromedriversesystemexecutable–布尔标志，使用随 Appium 安装的默认 Chromedriver
，避免任何寻找正确 Chromedriver 的尝试。
disable window animation–在设备上禁用窗口动画(现在在 UiAutomator 和 UiAutomator2 上可用
)。
page load strategy–chrome driver 的页面加载策略。
允许安装仅用于测试的 apk。
修复查找元素的隐式等待处理。
更好地处理 Unicode IME 安装。
放松包验证逻辑。
修复 UiAutomator 搜索中嵌套引号的错误。
需要时在 UiAutomator2 上执行辅助功能刷新。
改进确定应用程序是否需要升级的逻辑。
修复屏幕录制，允许更长时间的录制，最长可达 30 分钟。

Appium 1.9.0 的主要挑战

现在，我们向您介绍我们在此次发布中发现的挑战领域。

即使在使用 automationName 作为 uiautomatortool 功能后，我们也无法在> =Android v8.0 设备
中找到该对象。如果您已经找到了解决上述问题的任何方法，请在评论部分与我们分享。