# 鼠标悬停时的 IMDB 评级

> 原文：<https://dev.to/dhilipkmr/imdb-rating-on-mouse-hover-1ij6>

[![](img/fa143735ade3512d426f5805e9b2c8cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bRG7R21C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1fsofsstzhgnhillmrw7.png)

# 让我们做一个谷歌浏览器扩展

## 先决条件:

*   基本的 Javascript 知识
*   Chrome 浏览器

我们将建造什么？

独立于网站的 chrome 扩展

[![](img/07e7b2cf3661e1a062d7c9906ca583b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V5lqjRsX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ey5k2671nwzhuwzt1607.gif)

## 问题陈述:

有时候，我们花几个小时试图通过**手动**谷歌搜索来搜索不同电影的 **IMDB 评分、时长和情节**。

这是一个严重的问题

*   已经是星期天晚上 11 点了。
*   在你上床睡觉之前，你只有两个小时的黄金时间，你必须绝对确定你不是在浪费时间看一些无聊的电影。

[![](img/97f4073db9802d8b67e3e6e371507b61.png)](https://i.giphy.com/media/l0HU8V1CHKTUFtuFO/giphy.gif)

那你是做什么的？在悬停扩展上安装 [IMDb 额定值。](https://chrome.google.com/webstore/detail/imdb-ratings/aodmmndimojddogmhmpaemocbibnimkl?authuser=1)

## 扩展是如何工作的？概述

*   在导航到任何网页时`background.js`监听扩展上的点击事件。
*   点击后，`background.js`向`content.js`发送消息。
*   然后，`content.js`监听`hover`事件。
*   当用户将鼠标停留在任何文本上 250 毫秒时(举例来说),然后获取该位置的文本并进行 API 调用。
*   在获得成功的响应后，从中形成一个模板(HTML ),并在 UI 上填充它。

[![](img/9daa8d9b2049442e8ec4da98debbec1f.png)](https://i.giphy.com/media/UUVqDm2xhyU36/giphy.gif)

嗯，信息太多了。我知道！让我们分解它，看看它的行动。这将是一个 2 部分系列。

## 第一部分

让我们开始实施。

### 第一步:构造 manifest.json

```
{
  "manifest_version": 2,
  "name": "IMDB Ratings",
  "version": "0.1",
  "content_scripts": [
    {
      "matches": [ "<all_urls>" ],
      "js": ["content.js"]
    }
  ],
  "web_accessible_resources": [
    "styles.css"
  ],
  "background": {
    "scripts": ["background.js", "config.js"]
  },
  "browser_action": {
    "default_icon": "imdb.png"
  },
  "permissions": [
    "contextMenus"
  ],
  "icons": {
    "16": "imdb-16x16.png",
    "128": "imdb-128x128.png"
  }
} 
```

要注意的属性:

*   `content_scripts`:每当浏览器 URL 与`matches`下指定的 URL 匹配时，执行该属性中指定的脚本。因为我们需要它对所有的 URL 都可用，所以我使用`<all_urls>`作为它的值。
*   `web_accessible_resources`:通过脚本加载任意内容。
*   `background script`:浏览器打开时在浏览器后台运行的脚本。

### 第二步:构造 background.js 监听扩展点击。

```
/* clicking the addon icon */
chrome.browserAction.onClicked.addListener(buttonClicked);
function buttonClicked(tab) {
  var msg = {
    type: 'clickResponse',
    buttonClicked: true
  }
  chrome.tabs.sendMessage(tab.id, msg);
}; 
```

> ***注意*** : *`background scripts`没有访问 DOM 的权限，所以要向`content_scripts`求助。*

我们不希望我们的`content.js`脚本在用户导航到的所有选项卡中都是活动的。因此，我们将等待用户表明他希望我们的扩展处于活动状态。我们通过监听`background.js`中的点击事件来做到这一点。每当用户点击扩展，我们就给`content.js`发送一条消息，告诉他用户已经点击了它。

这里的 Click listener 接受一个带有参数`(tab)`的`callback(buttonClicked)`，该参数给出了当前标签的属性。我们通过`sendMessage` API 向`content.js`发回**消息**，并传递 tab 的 id 和消息。消息的类型有助于识别消息的类型。

> ***记住:*** *`content.js`不要访问浏览器点击或标签点击而是 DOM。*

### 第三步:通过 content.js 监听用户动作(悬停)

```
chrome.runtime.onMessage.addListener(hasGotMessage);
function hasGotMessage(response, sender, sendResponse) {
  switch (response.type) {
    case 'clickResponse':
      window.sessionStorage.setItem('_imdbRaterEnabled', "1");
      listenToMouseover();
      break;
  }
} 
```

为来自`background.js`的消息在`content.js`中添加一个**监听器**

当用户点击扩展时，我们从`background.js`得到一条消息。找到你从信息中得到的类型。如果类型是`clickResponse`，那么我们开始通过`listentoMouseover`监听 DOM 中的悬停事件

你可以试试看是否能得到那个信息。

第二部分是[这里的](https://dev.to/dhilipkmr/imdb-rating-on-mouse-hover-ii-2oa5)

[![](img/21f981994fa4d21050fbd72888b988dc.png)](https://i.giphy.com/media/3oEduEwqEfvGqetRXG/giphy.gif)

关注我以获取您订阅源中的有趣内容:P