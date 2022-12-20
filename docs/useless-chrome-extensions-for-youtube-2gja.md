# YouTube 上无用的 Chrome 扩展

> 原文：<https://dev.to/wynterjones/useless-chrome-extensions-for-youtube-2gja>

你好 Dev.to 社区！

我想和你分享一些无用的代码来创建各种各样的 Chrome 扩展，这些扩展可以在 YouTube 上播放。

这个想法主要是想看看如何使用 chrome 扩展将 CSS 和 JavaScript 注入页面。非常基础简单。

唯一的问题是，我要用这种力量做什么？

### **想法 1——隐藏 YouTube 评论**

首先，这里是注入 CSS 的基本`manifest.json`文件。

```
{
  "name": "Replace YouTube Comments",
  "version": "0.0.1",
  "manifest_version": 2,
  "description": "",
  "homepage_url": "http://www.monetizedesign.com",
  "default_locale": "en",
  "content_scripts": [
    {
      "matches": ["http://*/*", "https://*/*"],
      "css": [
        "inject.css"
      ]
    }
  ],
  "permissions": [
    "tabs", "http://*/*", "https://*/*"
  ]
} 
```

对于 chrome 扩展，你基本上需要`manifest.json`、`locales/en/messages.json`和额外的文件来处理你的扩展。

对于第一个例子，这里是隐藏 YouTube 评论的 CSS。

```
ytd-comments {
  display: none !important
} 
```

太神奇了！

相当无聊，尽管 318，626 名用户可能会有不同的想法，并称赞这个非常简单的 chrome 扩展(或在评论中争论功能)，正如这里看到的[隐藏 YouTube 评论扩展](https://chrome.google.com/webstore/detail/hide-youtube-comments/kehdmnjmaakacofbgmjgjapbbibhafoh?hl=en)。

* * *

下一个可能听起来很傻，当我向其他人展示这个想法时，他们最初认为我是一个巨魔...这由你决定。

### **想法二——自动厌恶 YouTube 视频**

如果你像我一样，看了太多 YouTube 视频，你可能会认为这是无用的，事实也的确如此。

就我个人而言，我很少喜欢、评论或订阅任何 YouTube 视频，但是因为 90%的 YouTube 视频要求你喜欢和订阅；我想为什么不自动不喜欢我正在看的每一个视频呢？

下面是将 JavaScript 注入页面的`manifest.json`。

```
{
  "name": "Auto Dislike YouTube Videos",
  "version": "0.0.1",
  "manifest_version": 2,
  "description": "",
  "homepage_url": "http://www.monetizedesign.com",
  "default_locale": "en",
  "content_scripts": [
    {
      "matches": ["http://*/*", "https://*/*"],
      "js": ["jquery.js", "inject.js"],
      "run_at":"document_end"
    }
],
  "permissions": [
    "activeTab"
  ]
} 
```

是的，我包括了 jQuery，主要是因为我懒。请随意用 vanilla JS 重写代码，如果我真的要发布这个扩展，我会这么做。

下面是一段代码，它总是检查并随时准备不喜欢任何 YouTube 视频:

```
function checkAndDislike() {
    setTimeout(checkAndDislike, 5000)
    if ($('ytd-app[is-watch-page]').length) {
      if (!$('#menu ytd-toggle-button-renderer').eq(1).hasClass('style-default-active')) {
        $('#menu ytd-toggle-button-renderer').eq(1).trigger('click')
      }
    }
}
setTimeout(checkAndDislike, 5000); 
```

现在这看起来很残酷，也很不寻常。

就无用性而言，这与我的下一个想法相比不算什么...

* * *

### **想法三——自动点赞并订阅 YouTube 视频**

这可能比其他无用的 chrome 扩展更具社会评论性，然而这个包含相同`manifest.json`的扩展的目标是做相反的事情。

自动喜欢和订阅您观看的每一个 YouTube 视频！

代码如下:

```
function checkAndSubscribe() {
    setTimeout(checkAndSubscribe, 5000)
    if ($('ytd-app[is-watch-page]').length) {
      const likeButton = $('#menu ytd-toggle-button-renderer').first()
      if (!likeButton.hasClass('style-default-active')) {
        likeButton.trigger('click')
      }
      const subscribeButton = $('#subscribe-button paper-button.ytd-subscribe-button-renderer')
      if (!subscribeButton[0].hasAttribute("subscribed")) {
       subscribeButton.first().trigger('click')
      }
    }
}
setTimeout(checkAndSubscribe, 5000); 
```

很邪恶，不是吗？

不，我说的不是需要注入 jQuery 来使用它只是为了使用`.first()`或者`.eq()`...

基本上每隔 5 秒钟，它就会查看页面来喜欢和订阅你正在观看的视频。

多么不可思议的无用。

不管怎样，代码不仅仅是函数没有用。

### 你有没有无用的 Chrome 扩展想法？

请在评论中告诉我一些没有实际用途的随意而愚蠢的想法。