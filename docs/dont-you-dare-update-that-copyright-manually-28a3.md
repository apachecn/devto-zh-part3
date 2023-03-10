# 你敢手动更新版权吗

> 原文：<https://dev.to/tonytalkstech/dont-you-dare-update-that-copyright-manually-28a3>

给开发者一个快速的提醒:现在我们进入了新的一年，如果你手动更新你的版权为“2019 ”,几乎可以肯定地狱里有你的位置。不要拖延时间——用类似`DateTime.UtcNow.Year`(或者你今年选择的任何语言)的东西永远解决这个问题。

我刚刚批准了一个将`2016-2018`更改为`2016-{DateTime.UtcNow.Year}`的请求，因为过去有三个单独的拉请求没有解决这个问题。除非有任何系统性的改变。NET 计算当前的 UTC 日期时间，我们将**再也不用更新这个了**。

### 2016 年 7 月 12 日

[![2016 Change](img/2f8e7ca6fd5321961f92e61fc12bdc72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TCu1nlYp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xaypxjiawux8lrboo7hn.png)

### 2017 年 2 月 16 日

[![2017 Change](img/8a2af29d5720f5ea99f274a0c8589549.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xAki0ws---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ma1hirwwtbmhwqh6p4y.png)

### 2018 年 7 月 13 日

[![2018 Change](img/8639f7d965d22a25a0c5318d9ffc1a76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ub_xbZol--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n3osm8cwvmyjj3ul3g4p.png)

### 2019 年 1 月 17 日

[![2019 Change](img/0790456bab97e5f6998eba5a5aa99a56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3VZZGwkr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qnmwz6qwclrx0hj74ki3.png)