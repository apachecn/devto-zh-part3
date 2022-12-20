# 如何在不使用 Route53 的情况下为 EC2 实例设置自定义域

> 原文：<https://dev.to/maybebored/how-to-set-up-a-custom-domain-for-your-ec2-instance-without-using-route53-f9>

在我之前的文章中，我解释了如何使用 AWS 部署一个简单的 WordPress 站点。这篇文章展示了如何设置一个自定义域来访问你的网站。访问 EC2 实例的默认方式是在浏览器中键入其 IP 地址( *192.168.0.1* )。这对于测试来说很好，但是当你的博客或作品集网站上市时，用一个域名比如“example.com”更有意义。你可以在本帖之外了解更多关于 [DNS 和 IP 地址](http://bpastudio.csudh.edu/fac/lpress/471/hout/netech/dns.htm)的信息。

AWS Route53 是一种基于网络的域名解析服务，具有额外的可靠性和可扩展性。它易于使用，灵活，非常适合初学者。然而，这篇文章是为那些(像我一样)不希望使用 Route53 的人准备的。

### 步骤 1 -设置弹性 IP

[![allocate new IP address](img/f1eda0218ffc7ba8e2c825bfb1b519c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tXkkp6rM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1.awsstatic.com/Getting%2520Started/launch-an-app/get-a-domain/Getting-Started-EIP1.4e0ed611d18a8bba0c9a97a1bff19595c6b51803.png)

在 AWS 管理控制台上导航到 EC2 >网络和安全>弹性 IPs，然后单击分配新地址。

[![associate new IP address begin](img/2487dff49c054ac022ac8a3f5c02ee2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lDolKfWh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d1.awsstatic.com/Getting%2520Started/launch-an-app/get-a-domain/Getting-Started-EIP4.a981d9bbdf876d00aab66ce33c9d0e5b01c50c59.png)

点击下一个屏幕上的“分配”。确认后你会看到你新创建的弹性 IP。选择此 IP >操作>关联地址。

[![associate new IP address end](img/b605725662abc34117d931a6560464d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gXtHzaiL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wm5h2lpd0vf5iygd8rrb.png)

从下拉列表中选择您的实例，然后单击关联。现在，您的 EC2 实例有了一个弹性 IP 地址。这意味着您有一个静态 IP，即使在重启实例后也不会改变。这对完成我们的下一步很重要。

通过导航到此弹性 IP 地址，验证您可以看到您的网站。

### 步骤 2 -更新您的域名的 DNS 记录以指向您的弹性 IP

[![DNS records](img/e87ccae2cd0f2b188d6eca3ac894499d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Juad9mBz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/brpltr6rk4obrzvznms0.png)

创建[为您的域键入一个 DNS 记录](https://pdrcybersecurity.com/dns-request-types-cheat-sheet/)，它指向您的弹性 IP。如果没有弹性 IP，每当您重启 AWS 实例时，我们都必须不断更新这个记录。

创建新的 DNS 记录后，可能需要 5 分钟到 1 小时的时间来更新。⏳

### 步骤 3 -更新 EC2 实例的域设置

通过 SSH 登录到 EC2 实例，然后在 apps/WordPress/htdocs/WP-config . PHP 文件中指定您的域名，如下所示。

替换

`define('WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST'] . '/');
define('WP_HOME', 'http://' . $_SERVER['HTTP_HOST'] . '/');`

`define('WP_SITEURL', 'http://DOMAIN/');
define('WP_HOME', 'http://DOMAIN/');`

仅此而已。你应该能够在你的域名地址访问你的 WordPress 站点。🎉

请在评论中告诉我你对这篇文章的看法。我想提高我的写作水平，并帮助其他人学会喜欢科技，就像我一样。