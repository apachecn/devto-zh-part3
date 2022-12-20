# 调用 JavaScript 库:“导航历史”

> 原文：<https://dev.to/ben/call-for-a-navigation-history-library-2k43>

基于这篇文章的讨论:

[![ben](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 有什么方法可以在 PWAs(特别是桌面 PWA)中检测用户是否“可以返回”

### 本哈尔彭 5 月 22 日 1911 分钟阅读

#help #javascript #webdev #pwa](/ben/is-there-any-way-to-detect-if-a-user-can-go-back-in-pwas-desktop-pwa-most-specifically-203j)

我们找到了这条线索:

[![stereobooster profile image](img/85dd06cbd01e5925246fdc462edccfe5.png) ](/stereobooster) [ stereobooster ](/stereobooster) • [<time datetime="2019-05-22T18:26:38Z" class="date-short-year"> May 22 '19 </time> • Edited on <time datetime="2019-05-22T18:30:30Z" class="hidden m:inline-block date-no-year">May 22</time>](https://dev.to/stereobooster/comment/b2jh) 

也许你在找`window.history.length`？ **UPD:** 只是再检查一遍，当你使用后退按钮时它不会减去数字:/

[![kognise profile image](img/58efd0c4947e5e992735b0fae9a35f76.png) ](/kognise) [ Kognise ](/kognise) • [<time datetime="2019-05-22T19:26:48Z" class="date-short-year"> May 22 '19 </time>](https://dev.to/kognise/comment/b2ke) 

也许有一种方法可以让编程变得神奇并发挥作用？

[![ben profile image](img/aafbcbcc2891483db855a7b6ec39b85c.png) ](/ben) [ Ben Halpern ](/ben) • [<time datetime="2019-05-22T21:39:15Z" class="date-short-year"> May 22 '19 </time>](https://dev.to/ben/comment/b2mc) 

在这一点和其他持久性(如`localStorage`等)之间，可能有一种技术上可行的方法来实现它。我们只需要担心*现场*的情况，因为这是针对独立应用的。

这是一个相当普遍的情况，所以如果有人想为此建立一个库，我完全支持！😄

我相信所有关于确定`cangoback`的信息都存在于关于过去在站点上采取的行动的信息中，并且以任何需要的方式持续存在。

如果有人想尝试一下，这将是非常有用的！