# 让懈怠不那么让人分心

> 原文：<https://dev.to/cohan/make-slack-a-little-less-distracting-470k>

在 Slack 的浏览器版本中，如果出现任何消息，favicon 将显示为白色。这将禁用该功能，保持红色的新邮件图标不变

要做到这一点，你需要一个浏览器拦截器(我强烈推荐 [uBlock Origin](https://getublock.com/) ，但是任何支持 Adblock 风格的个人拦截列表都可以)

## 针对特定的备用服务器

在您的个人过滤器列表中，使用您的 Slack 服务器的名称添加以下 URL 替换实例

`https://INSTANCE.slack.com/img/ico/app_icon_32px_online_unreads.png`

## 针对所有备用服务器

同样的事情，但在你的个人过滤器添加网址

`https://a.slack-edge.com/5d94d/img/ico/app_icon_32px_online_unreads.png`

## 搞定

现在，当一条不需要你注意的消息进来时，你不会看到令人分心的白色图标，但仍会看到红色图标，表明你有一条新的直接消息。噪音。