# 本地加载的扩展与商店安装的扩展之间的差异

> 原文：<https://dev.to/spekulatius1984/differences-between-locally-loaded-vs-store-installed-extensions-3k5d>

在[临时加载的浏览器扩展](https://browserextension.dev)和通过 Chrome 网络商店/Mozilla 插件商店安装的扩展之间有一些微小但通常很重要的区别。这些可能很难调试，因为它们只在有限的情况下出现。以下是我在开发浏览器插件时发现的一些不同之处:

## Chrome 和 SVG 图标不太协调

`Chrome`、`SVG`、`Happens-only-on-Webstore`

随着我的扩展的第一个版本的公开发布，我注意到有些人在安装时收到“无法解码图像:‘icon . SVG’”的通知。我解决这个问题的第一个尝试自然是确保 SVG 是有效的，并且它在开头包含一个有效的 XML 标签`<?xml version="1.0" encoding="UTF-8"?>`。不幸的是，这并不成功。Chrome 似乎就是不喜欢 SVG 图标。我决定通过用 PNG 图像替换 SVG 图标来解决这个问题。SVG 图像可以很容易地使用免费的在线服务进行转换，如 svgtopng.com T2。在更换并等待 Chrome 团队的审查后，问题最终得到了解决。

## Firefox 本地存储 API 与生产中

`Firefox`、`Storage API`、`Happens-locally-loaded`

临时安装扩展时，存储 API 可能无法按预期工作。重新启动 Firefox 并重新安装你的临时扩展，Firefox 会给你的插件分配一个新的 ID(标识符)。此唯一标识符用于命名本地和同步存储。变化的结果是，您的存储在没有任何明显原因的情况下自行清除。唯一的提示是隐藏在后台 JS 的控制台。在那里，您可能会发现以下消息:

`Error: "The storage API will not work with a temporary addon ID. Please add an explicit addon ID to your manifest. For more information see https://bugzil.la/1323228."`

通读[错误报告](https://bugzil.la/1323228)暗示了如何解决这个问题。可以通过在 manifest.json 中添加以下键来解决这个问题:

```
"applications": {
  "gecko": {
    "id": "whichlogin@peterthaleikis",
    "strict_min_version": "53.0"
  }
}, 
```

Enter fullscreen mode Exit fullscreen mode

“id”可以是

*   电子邮件地址，
*   类似于`project@yourname`或
*   一个 UUID。

确保避免真实的或无所不包的电子邮件地址，因为您可能会成为垃圾邮件发送者的目标。添加“应用”键会导致 Chrome 发出警告`Unrecognized manifest key 'applications'`。预先装配可以忽略此警告。

## 发现更多问题？请伸出手来！

随着我发现新的差异，我将扩展这个列表，以帮助其他浏览器扩展开发人员🙏️，如果这篇文章对你有帮助，请考虑在 Twitter 上分享。有没有发现另一个不同点，想补充一下？在 Twitter 或 Telegram 上大声喊出来(见联系人)，我就加进去👍️