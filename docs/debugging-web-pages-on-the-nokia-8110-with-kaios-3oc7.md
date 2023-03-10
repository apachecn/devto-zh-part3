# 使用 KaiOS 调试诺基亚 8110 上的网页

> 原文：<https://dev.to/chromiumdev/debugging-web-pages-on-the-nokia-8110-with-kaios-3oc7>

我们最近在功能手机上做了很多开发，这很难，但很有趣。最难的是，在 KaiOS 上，我们发现无法调试网页，尤其是在我们现有的硬件上(诺基亚 8110)。诺基亚是一个很棒的设备，它是用 KaiOS 构建的，我们知道它是基于类似于 Firefox 48 的东西，但它被锁定了，没有像其他 Android 设备那样的传统开发模式，这意味着你可以连接 Firefox 的 WebIDE。

通过阅读一些博客，并对`adb`有所了解，我想出了如何去做。请注意，其他人可能已经能够做到这一点，但它没有清晰地记录在一个地方。

[![](img/d5367f5e94298199b8e2d40837018892.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CXeDtJCW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://paul.kinlan.img/2019-03-21-debugging-web-pages-on-the-nokia-8110-with-kaios.jpeg)

(上图显示了 DevTools 和截图工具的输出)

以下是步骤:

1.  连接 USB 电缆。确保您的主机上安装了`adb`。
2.  下载一份火狐 48 的拷贝(这是我唯一能找到的)
3.  通过从手机输入`*#*#33284#*#*`启用“开发者模式”(注意，不要使用拨号器)。你会在屏幕上方看到一个小小的“bug”图标。来源
4.  连接您的 USB 电缆
5.  在您的开发机器上运行以下命令
    1.  `adb start-server`
    2.  `adb devices`检查手机是否已连接。
    3.  这将建立一个从您的机器到手机插座的通道。这就是 Web IDE 所使用的。
6.  打开 Firefox，进入工具，然后进入 Web IDE
7.  Web IDE 将会打开，单击“远程运行时”并单击“打开”按钮，其中包含“localhost:6000”。(这是 tcp 转发端口)。
8.  在手机上打开一页，你应该会在左边看到它。