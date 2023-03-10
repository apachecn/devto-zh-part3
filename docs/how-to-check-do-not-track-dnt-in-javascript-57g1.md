# 如何在 JavaScript 中勾选请勿追踪(DNT)？

> 原文：<https://dev.to/spekulatius1984/how-to-check-do-not-track-dnt-in-javascript-57g1>

## 什么是不跟踪？

“不跟踪”是用户不希望被谷歌分析或类似服务跟踪的偏好。如果处于活动状态，它将作为 HTTP 报头发送，所有请求都将发送到服务器。根据您使用的浏览器，默认情况下，在私人/匿名会话中会打开此功能。

## 如何用 JavaScript 检查 DNT 是否设置？

在某些情况下，当有人访问你的网站时，你可能需要找出 DNT 是否是活跃的。例如，如果您使用的是[静态站点生成器](https://peterthaleikis.com/posts/introduction-to-static-site-generators/)并且需要在中编译 g a 代码，但只有当您的网站访问者同意时才激活它，就可能出现这种情况。以下代码片段允许您检查 DNT 是否是使用 JavaScript 设置的:

```
let dntActive = () => {
  // get the value from the various browser implementations.
  let dnt_active = parseInt(
    // Internet Explorer 9 and 10 vendor prefix
    navigator.msDoNotTrack ||

    // IE 11 uses window.doNotTrack
    window.doNotTrack ||

    // W3C
    navigator.doNotTrack,
    10
  );

  // If this comes to exactly 1 DNT is set.
  return (dnt_active === 1);
}

// Check if DNT is active.
if (dntActive()) {
  // active
} else {
  // inactive
} 
```

按照这个例子，你应该找出一个用户是否不喜欢被跟踪。

这个快速技巧的灵感来自于[https://github . com/saber land/saber/blob/master/packages/saber-plugin-Google-analytics/saber-browser . js](https://github.com/saberland/saber/blob/master/packages/saber-plugin-google-analytics/saber-browser.js)🙏️