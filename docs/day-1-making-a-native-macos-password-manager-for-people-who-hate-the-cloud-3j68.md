# 第一天:为讨厌云的人制作一个 swift macOS 密码管理器

> 原文：<https://dev.to/swlkr/day-1-making-a-native-macos-password-manager-for-people-who-hate-the-cloud-3j68>

这个月，我正在为讨厌云的脾气暴躁的老人制作一个原生 macOS 密码管理器，我将每天在博客上记录我的进展。让我们开始吧。

[![](img/5eec3c040c41f0b4b1586a9f710add39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n_JUbmY1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.kym-cdn.com/photimg/newsfeed/001/044/247/297.png) 
那就是我

## 特性

我可能应该把特色和东西摆出来，因为我是一个很棒的策划人，而且我也很有条理？所以这很好。特点:

*   一个密码就能统治所有人
*   只有 swift + xcode + macos，没有 js，没有 electronic，没有 v8，没有 google，没有 chrome，没有不必要的电池消耗，黑客新闻投票上升
*   没有云，没有服务器，全是客户端！
*   开源是因为我爱在业余时间免费做东西！
*   通过 app store 和网站一次/一生 29 美元，因为我喜欢用$美元在业余时间制作东西，没有比停止订阅并给我你辛苦赚来的钱更好的方式了！

*奖励功能*

*   ios 应用程序(一个月后，我是什么某种迅捷的超级英雄？是啊没错)
*   通过局域网或蓝牙在后台或按需同步到 ios？

## 我今天做了什么

这是第一天(就像在亚马逊！)各位，那么我今天做了什么？

*   我醒来，喝了一杯咖啡，把咖啡豆拿出来再磨一次
*   在 Xcode 中创建了一个新项目
*   审查新的苹果开发者服务条款或协议或 developer.apple.com 的东西

...这就是我今天到目前为止所做的，现在是下午 12:09，绝对取得了很大的进步。太好了，明天继续收看。不，我开玩笑的，我们继续。

## 制作带有文本字段的屏幕

现在我已经打开了 Xcode 并启动了一个新的 swift...咳咳，可可项目，没有什么比一张空白的画布更好的了。好的，第一步，创建一个超级难看的屏幕，用一个文本字段代表一个密码:

[![allyourpasswords v0](img/f8078797062fcbf19c73d86064ff78ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uKd_8rqa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/xfty7uR.png)

当然，现在我已经这样做了，这没有任何意义，因为我必须先设置一个密码，并将其存储在 sqlite 中。做我自己真好。我今天花了大部分时间试图让一个密码字段有更大的文本，这是苹果制造的困难...太好了。难怪人们使用电子。

明天，同样的时间，同样的地点继续收听，让设置页面工作起来！