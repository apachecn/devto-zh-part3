# 我做了一个 Chrome 扩展。松弛渠道分组

> 原文：<https://dev.to/yamadashy/slack-channels-grouping-i-made-a-chrome-extension-258b>

太多的频道让眼睛痛苦😫我做了一个 Chrome 扩展，很好地将频道分组。

| ![](img/799f95306583e5d447a71c974aab1397.png) | ![](img/164dbefcc4976d2c28a95d5dda328c5e.png) |
| 

<center>原件</center>

 | 

<center>分组</center>

 |

在此安装

*   Chrome: [Slack 频道分组- Chrome 网络商店](https://chrome.google.com/webstore/detail/slack-channels-grouping/lcbnhfianneihfgkmfncnhpkpghedbkm?authuser=0)
*   Firefox: [Slack 频道分组——获得以下扩展🦊火狐](https://addons.mozilla.org/firefox/addon/slack-channels-grouping/)

# 特征

它通过连字符和下划线将频道分组。

*   动物狗
*   动物 _ 猫

它观察 DOM 更新，因此它支持频道创建和重命名。
如果选项卡未激活，DOM 观察关闭。

# 为什么

在我的团队中，有一条规则是在频道名称中添加“前缀-”。随着频道数量的不断增加，很难区分“聊天”和“俱乐部”🤔

所以我把它做得尽可能的视觉友好。

# 关于发展

我写的是以下几个关键词。

*   web 扩展-工具箱
    *   网络包
    *   以打字打的文件
*   页面可见性 API
*   requestIdleCallback

## web extension-工具箱

webextension-toolbox 是 Chrome、Firefox 等的扩展创建工具。
[web extension-工具箱/web extension-工具箱- GitHub](https://github.com/webextension-toolbox/webextension-toolbox)

你可以轻松输出 Chrome 和 Firefox 扩展，而不用考虑跨浏览器支持。

使用以下命令生成一个模板，

```
yarn global add yo generator-web-extension
yo web-extension 
```

开始开发，

```
yarn dev chrome 
```

输出 zip，

```
yarn build chrome 
```

只需上传到 [Chrome 网络商店仪表板](https://chrome.google.com/webstore/devconsole/)。

这太容易了！

### webpack

webextension-toolbox 用 webpack 构建，但是即使你从未接触过 webpack 也可以开发。如果你熟悉 webpack，你可以很容易地扩展它。

### 打字稿

最近我一直在积极使用 TypeScript，我也使用了它。

如果使用 typescript 和 webextension-toolbox，需要创建一个文件`webextension-toolbox-config.js`来覆盖配置。

```
module.exports = {
  webpack: (config, { dev, vendor }) => {
    config.resolve.extensions.push('.ts')
    config.module.rules.push({
      test: /\.ts$/,
      loader: 'ts-loader'
    })
    return config
  }
} 
```

如果是大规模的扩展，TypeScript 的好处是很大的。

## 页面可见性 API

这个 API 观察页面是否可见。
[页面可见性 API-Web API | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API)

这个扩展使用 MutationObserver 观察 DOM。我不想让它继续工作，所以我用这个 API 来控制它。

```
document.addEventListener("visibilitychange", () => {
  if (document.hidden) {
    // Stop observing
  } else {
    // Restart observing
  }
} 
```

## 请求回调

这个 API 在事件循环空闲时执行处理，这样它就不会阻塞其他处理。这篇文章很好地介绍了这个 API。
[使用 requestIdleCallback | Web | Google 开发者](https://developers.google.com/web/updates/2015/08/using-requestidlecallback)

```
requestIdleCallback(() => {
  // execute
}, {
  timeout: 3000 // Forced execution if not executed after 3 seconds
}) 
```

该 API 用于对频道列表进行分组。

# 最后

第一次知道 webextension-toolbox，但是很方便，很棒。
可以快速创建跨浏览器扩展，快来试试吧！😄