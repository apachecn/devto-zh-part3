# Android 应用程序扫描 QR 并更新 firebase 中的数据，然后显示出来

> 原文：<https://dev.to/akshay090/android-app-to-scan-qr-and-update-data-in-firebase-and-display-it-18dd>

在我们开始之前，这里有一些我们正在构建的应用程序的用户界面的图片，这可能有助于你理解正在发生的事情。

| 默认(无互联网) | 扫描仪 | 已加载详细信息 |
| --- | --- | --- |
| ![](img/31ec596294ce95c6f15c5c1d1e595a70.png) | ![](img/c0f2f44b7b14407bd550025c039315cb.png) | ![](img/54843c3ed4e7c61d8f496231059c0645.png) |

为了实现 QR Scanner，我使用了 zxing 库，按照我的说法，这是最好的方法。

这个的代码在

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [【俱乐部-债务字节】](https://github.com/Club-DevBytes)/[【formqr】](https://github.com/Club-DevBytes/formiQR)

### 在 firebase 云功能的帮助下，利用 google form 成为一个完整的事件管理平台

<article class="markdown-body entry-content container-lg" itemprop="text">[![](img/ea47d9400925c09e955c8aaede079664.png)](https://github.com/Club-DevBytes/formiQR/blob/master/G-Form/formiQR.png)

# formiQR

## 在 firebase 云功能的帮助下，利用 google form 成为一个完整的事件管理平台

# formiQR 解决的问题

如果你参加过任何黑客马拉松或类似的活动，最初的时间都浪费在了注册台，我们需要说出自己的名字，然后他们会四处搜索。但是有了 formiQR，它可以在 QR 扫描的瞬间发生，再加上它可以与 google form 一起工作，还有什么比这更棒的呢😄

# 什么是 formiQR？

## 总体思路

*   首先，用户填写谷歌表单，并立即收到一个带有入职电子邮件的二维码。
*   在活动当天，用户向组织者出示 QR 以确认出席。
*   组织者可以看到与会者在会场的实时统计数据，具体细节最初填写在表单中，如年龄组、性别、年级等。

## 为什么

…</article>

[View on GitHub](https://github.com/Club-DevBytes/formiQR)

你应该看看实时数据库的结构，这可能会有帮助

[![](img/35a97863b6e9a3344e6358765d26344c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9M_VfrgC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mkw6ykisl8ojkdneydh2.png)

如果你是 firebase 和 android 的新手，你可以查找如何连接它们，关于这个话题已经有足够多的内容了。

如果你需要任何帮助，请在评论中提问。