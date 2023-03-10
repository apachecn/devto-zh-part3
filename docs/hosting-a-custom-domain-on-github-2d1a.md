# 在 Github 上托管自定义域

> 原文：<https://dev.to/vjnvisakh/hosting-a-custom-domain-on-github-2d1a>

所以，我们都曾想做一个静态网站，却不知道从哪里购买。通常，它包括两个步骤-

1.  从哪里获取域名。
2.  从哪里获得托管。

尽管市场上有很多竞争对手，但选择一个最好的又变得令人头痛。

但是真正的问题是**你需要它吗**

在当今世界...我指的是互联网世界，所有东西都很便宜，你只需要找到合适的商店就可以了。

**域名**

1.  前往 [freenom](https://www.freenom.com/en/index.html?lang=en) 。一个提供免费域名的地方。
2.  Nextraone 也是一个价格合理的网站，我用它来购买域名。
3.  然后像往常一样还有 Godaddy。

**免费托管**

1.  像 Freehosting.com 或 Hostinger.com 的[或**这样的网站。**](https://www.freehosting.com)
2.  Github 页面等。

下面是我为托管我的小网站所做的事情

1.  从 Godaddy 那里买了一个域名叫做 [vjnvisakh.info](http://vjnvisakh.info/) 。不过，目前网站上什么都没有。:)

2.  然后我在 Github 上创建了一个资源库。

3.  由 index.html 将文件添加到回购协议中并将其推送到回购协议中。

4.  添加了一个只包含我的域名的 CNAME 文件。[![](img/64c4a84c66373e90c19706a3244cf59a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YAo4npQm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ttmeb8k8yk3c7basz0g7.png)

5.  去 Godaddy 更新了网站的 DNS 设置，如下图所示。[![](img/5c485b5a902f55c6c2f8ffdbf53ccbd4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z0eZp2eA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6gw89uez7x7slz51vpar.PNG)

6.  转到 Github 库(网络版)并使用 master 分支创建了一个 Github 页面。

7.  瞧啊。网站已更新。

当然，不要期望通过它运行成熟的 PHP 站点。但是对于静态网站来说，这是一种简单又便宜的方法。