# 用 Selenium 测试 Chrome 扩展

> 原文：<https://dev.to/razgandeanu/testing-chrome-extensions-with-selenium-491b>

你可以通过安装扩展为 Chrome 添加新功能，也可以构建自己的扩展。

快速浏览一下 Chrome 网上商店会发现你可以找到几乎任何东西的扩展。

[![Chrome Web Store](img/bdf458e782012ecff9822870405b0008.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eXLak_TS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vPuwFss.png)

令人惊叹的是，你可以利用 HTML、CSS 和 JavaScript 等核心技术为 Chrome 创建新的扩展。

如果您想为该扩展创建自动化测试，您可以使用 Selenium 轻松完成，我将向您展示如何操作。

我还将对 Selenium 和:
[https://www.youtube.com/embed/4DIVKcs--TA](https://www.youtube.com/embed/4DIVKcs--TA)进行一些比较

问题是 Selenium 仅限于浏览器的视窗，而扩展在视窗之外。
[![Chrome Extension Selenium](img/e46941f0325f0f833a177f82fca2ae8d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--1TAAA5ai--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/XrfrrYw.png)

这意味着我们需要一种偷偷摸摸的方式来访问该扩展，就像我们访问网页一样。

第一步是从谷歌网络商店安装 [Chrome 扩展源代码浏览器](https://chrome.google.com/webstore/detail/chrome-extension-source-v/jifpbeccnghkjeaalbbjmodiffmgedin)。

安装之后，从 Chrome 网上商店转到你的 Chrome 扩展的 URL，你会注意到右上角的 CRX 扩展图标变成了黄色。

[![Chrome Extension Selenium](img/0659451935339a0e82a6956fb649d9c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3r0oPWBa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/tZ6w8fO.png)

注意:CRX 也可能是一个没有上传到 Chrome 网上商店的本地文件。在这种情况下，你需要手动安装它，方法是将 CRX 文件拖入 **Chrome://extensions** 页面，然后点击 **Add** 。

现在，点击 CRX 黄色图标并选择**查看源**选项。
T3![CRX View Source](img/f609c2913f174b90712911f7fba59089.png)T5】

而这里的源码为 **Office Online** 扩展:
[![Office Online Chrome Extension Source](img/3708dae77fc5a9687db3f12cc8386258.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N7ZqrWo1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/vTYvDfk.png)

现在我们需要确定我们想要测试的页面。我们可以通过提供 URL 来访问该页面，URL 由扩展 ID 和页面名称组成。

**chrome-extension://ID/name _ of _ page . html**

转到 **chrome://extensions** 部分，查找您的扩展 ID。
T3![Chrome extensions](img/314747e16474cdb82845a317ab7aa81f.png)T5】

在我们的例子中，该页面的 URL 将是:
**chrome-extension://ndjpnladcallmjemlbaebfadecfhkepb/index . html**

在这里，我们现在可以在视窗中访问扩展:
[![Chrome Office Online extension](img/7a38bb187a36e417a05d63a217d5f7e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4tEjUFB5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/HVLyfe0.png)

这意味着我们可以检查元素并为它们获取相关的定位器。

从 **Chrome 扩展源码查看器**扩展中使用**下载为 zip 文件**选项下载 CRX 扩展。
[![CRX Download as zip](img/76c97fddfaa3715bc09c8d1d772025f2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4B9x98LZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/cnlHspE.png)

当启动 **chromedriver** 时，你需要用 **add_extension** 方法加载那个 ZIP 文件。

而这里是实际的 Python 代码:
[![Selenium Python code](img/62e0b1e52f2a413602a5a2d303802357.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bjPC3xXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/X1H51S7.png)

请注意，扩展加载了扩展的缺省页面，所以如果您想从不同的扩展页面开始测试，只需要添加 driver.get(url)步骤。

如果你懒得写代码，你可以用 [Endtest](https://endtest.io) :
[![Endtest Extension](img/4d62a26ea8014b3bf3cf36d97ea10100.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tJIdDKKf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/rYaK5fn.png)