# 一个朋友的地理网站

> 原文：<https://dev.to/titivermeesch/a-geoloc-website-for-a-friend-5dg6>

先说开头，一个厄瓜多尔的朋友钱包被偷了，我只知道他的名字。

所以我开始用谷歌搜索了一下他，找到了他的脸书，当然还创建了一个看起来最不合法的假账户。在等待的时候，让我们想想如何找回他的位置。

请记住，在某些国家，我接下来要做的事情不起作用。我和我的朋友一起测试了它，定位给了我她的准确坐标，而在比利时，它给了我 100 公里以外的位置。

我发现了一个非常酷的 Json API，它允许我获取与用于请求的 ip 相关联的本地化数据:[http://gd.geobytes.com](http://gd.geobytes.com)
因此，如果有人打开我的网站，通过简单的获取就可以很容易地获得信息。
现在问题有点棘手了，由于是静态页面，我又不想安装 node-js 后端，只要把文件上传到服务器主机上就搞定了，所以我决定把信息电邮给自己。

经过一点点研究，我发现了 post mail[https://postmail.invotes.com/](https://postmail.invotes.com/)，它工作得非常好。

我自己试过，我可以很容易地在我的邮箱里找到任何访问这个页面的人的信息。

GITHUB 回购:[https://github.com/titivermeesch/geoloc-website](https://github.com/titivermeesch/geoloc-website)

只有一个缺点，有人可以简单地提取我用来发送邮件的令牌，并为自己所用，但是的，这是我抓住小偷的风险。

感谢阅读。希望我的第一篇文章有趣。