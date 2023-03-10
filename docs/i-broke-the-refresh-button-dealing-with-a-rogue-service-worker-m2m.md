# 我打破了刷新按钮:处理一个流氓服务人员

> 原文：<https://dev.to/tvanantwerp/i-broke-the-refresh-button-dealing-with-a-rogue-service-worker-m2m>

上周我很高兴推出了一个网站的新版本。当周一到来时，人们仍在看旧版本时，我就不那么高兴了。标准的“你试过刷新”没有效果:不管家里的人刷新多少次，旧的内容会不断回来。这是一些侵略性的缓存！

罪魁祸首是一个旧的`service-worker.js`文件！旧站点已经建立了`create-react-app`，默认包含服务人员注册，我没有太注意。新网站是用[盖茨比](https://www.gatsbyjs.org/)建的，默认不包括一个。

服务人员可以帮助您缓存您的站点/PWA，以便它在脱机时仍能正常工作。通常，服务人员会在您 24 小时后访问相关网站时检查其自身的新版本。但是当旧的服务人员检查更新时，它得到 404 响应，并默认保持活动状态，无论如何都提供缓存内容。

为了让客户不再依赖旧的服务人员，我必须在站点的根目录(它期望找到它的地方)添加一个新的“updated”`service-worker.js`。

新服务人员，整体:

```
self.addEventListener('install', () => {
  self.skipWaiting();
}); 
```

这只是告诉浏览器:“嘿，我是新的服务人员，扔掉旧的服务人员，嗯，就这样，我们到此为止。”

因为这个特殊的站点从一开始就不需要服务人员，所以我很高兴这个无操作的服务人员是一个快速而肮脏的解决方案。在客户抓取这个来替换最后一个服务人员之后，简单的刷新将再次允许他们的浏览器实际上访问互联网来抓取东西，而不仅仅依赖于非常陈旧的缓存。

感谢 Jeff pos nick via[stack overflow](https://stackoverflow.com/a/38980776/9554333)提供了这个简单的解决方案！关于服务人员是如何工作的，我有很多要学习，并且对如果你在没有真正理解他们的情况下使用他们会发生什么有了新的认识

*[![jeffposnick image](img/7ea20d10e1fbb1fd5837afb892861d74.png)](/jeffposnick)

## 杰夫·波斯尼克

[I'm a member of Google's Web DevRel team, focusing on Progressive Web Apps.](/jeffposnick)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)【jeffposnick】](https://twitter.com/jeffposnick)[![external link icon](img/7ad9ad23055d49c106b927d92662ca16.png)【https://jeffy . info】](https://jeffy.info)*