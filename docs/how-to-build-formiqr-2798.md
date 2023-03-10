# 如何构建 form QR——借助 firebase cloud 功能利用 google form 成为一个完整的事件管理平台

> 原文：<https://dev.to/akshay090/how-to-build-formiqr-2798>

在这篇文章中，我将介绍什么是 form QR 以及如何构建 form QR。

首先，什么是 formiQR，它解决了什么问题？

formiQR 的目标是借助 firebase cloud 功能，利用 google form 成为一个完整的事件管理平台。

最初我开发 formiQR 是为了帮助一个朋友完成一项名为 MakerFest Vadodara 的活动。他们需要一个系统，在这个系统中，用户填写谷歌表单，然后收到一个个性化的二维码，他们可以在活动中扫描这个二维码，以确认他们的存在，并获得其他统计数据，如有多少人出席了活动，他们是什么年龄组，性别等。

现在，在成功完成这个项目后，我想到了组织所有的代码，并进行适当的回购，因为这可能会对某人有所帮助。

因此，最终产品的适当 git 回购位于[Club-dev bytes/form QR](https://github.com/Club-DevBytes/formiQR)，这是一篇逐步解释它的博客文章。

我计划将它分成 4 个部分:

*   将填写在 google 表单中的数据发送到 firebase，并在 google sheets 中使用触发器
*   [从云功能发送个性化邮件](https://dev.to/akshay090/sending-personalized-email-from-cloud-function-50al)
*   [实现计数器的云函数](https://dev.to/akshay090/a-cloud-function-to-implement-a-counter-4i24)
*   [Android 应用程序扫描二维码，更新 firebase 中的数据并显示出来](https://dev.to/akshay090/android-app-to-scan-qr-and-update-data-in-firebase-and-display-it-18dd)

# 所以让我们从第一部分开始:-)

## 将 google 表单中填写的数据发送到 firebase，并在 google sheets 中使用触发器

我们需要将链接到我们的 google 表单的 google sheet 中的数据发送到 Firebase，为此我们将使用 Google Apps Script，这是一种用于 G Suite 平台中轻量级应用程序开发的脚本语言。

为此，让我们切换到顶部栏中的“工具”>“脚本编辑器”

然后你需要在 Code.gs
中添加如下代码