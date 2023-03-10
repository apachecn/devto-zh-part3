# 雨果和 GitLab 的博客(4): HTTPS 与让我们加密

> 原文：<https://dev.to/loadbalancing/building-a-blog-website-4-https-with-let-s-encrypt-1j4f>

GitLab 的默认 HTTPS 支持将无法用于自定义域。幸运的是，有免费添加 HTTPS 回来的解决方案。

## 为什么是 HTTPS？

HTTPS (HTTP over TLS 或 HTTP over SSL)比 HTTP 更安全。如今，如果一个网站是通过 HTTP 而不是 HTTPS 访问的，许多浏览器会将其标记为不安全。我认为这对个人博客很重要，因为你不希望人们认为他们访问你的博客网站会面临安全风险。

## 为什么要加密？

为了给网站配备 HTTPS，您需要从浏览器信任的证书颁发机构(CA)获取 SSL/TLS 证书。好在有免费证书。例如:

*   CAcert 在 Linux 世界更受欢迎，但它颁发的证书仍然不被大多数浏览器信任。
*   [Cloudflare](https://www.cloudflare.com) (顺便说一句，他们拥有一个有趣的域名*https://one . one . one . one*)提供免费证书，但[如果你去全功能的话可能不会真的免费](https://nickjanetakis.com/blog/lets-encrypt-vs-cloudflare-for-https)。
*   [让我们加密](https://letsencrypt.org)提供全功能免费证书，有效期 90 天，可续期。

总的来说，还是加密最好。

## 环境

*   macOS Sierra。

## 获取一个咱们加密的证书

1.  在 macOS 中安装自制软件。
    /usr/bin/ruby-e " $(curl-fsSL[https://raw . githubusercontent . com/home brew/install/master/install)](https://raw.githubusercontent.com/Homebrew/install/master/install))

2.  安装 certbot。
    布鲁尔安装确定机器人

3.  申请证书。虽然是可选的，但建议提供电子邮件来接收通知(例如，证书过期)。这里我们请求根域和子域的证书。
    sudo cert bot certonly-a manual-d load balancing . XYZ-d[www . load balancing . XYZ](http://www.loadbalancing.xyz)-email[example@email.com](mailto:example@email.com)
    接下来是几个步骤:

    *   键入`A`同意他们的服务条款。
    *   键入`Y`或`N`，这取决于您是否希望接收来自“让我们加密”的新闻。
    *   键入`Y`同意记录您的 IP。
4.  然后在这里暂停(不要按回车键)。按照说明创建一个包含指定数据的文件，并将其提交到存储库中。
    CD your _ repository/static
    mkdir-p . unknown/acme-challenge/
    VI _ specified _ file _ name
    文件需要在:
    T5】http://loadbalancing.xyz/.知名/acme-challenge/the _ specified _ file _ name

5.  现在按回车键。它将生成证书和密钥并保存到本地。记录下它们的内容，这些内容将在下一步中使用。sudo cat/etc/lets encrypt/live/load balancing . XYZ/full chain . PEM
    sudo cat/etc/lets encrypt/live/load balancing . XYZ/privkey . PEM

## 将证书添加到 GitLab

1.  将证书和密钥添加到您的网站。

    【设置】>【页数】。

    对于每个域(根域和子域)，点击【详细】>【编辑】。

2.  将证书复制并粘贴到第一个字段`Certificate (PEM)`。

3.  将私钥复制并粘贴到第二个字段`Key (PEM)`。

4.  让交通自动导向 HTTPS。

    选择“强制 HTTPS(需要有效证件)”。

    点击【保存】。

几分钟后，您将能够通过 HTTPS 访问您的网站。您可能还想更新`config.toml`中的基本 URL。

## 续保

如前所述，让我们加密每 90 天到期的证书，您必须定期更新它们。要续订证书:

```
sudo certbot renew 
```

好吧，两个月后，我意识到上述更新只是不起作用。给出了以下错误:

*"尝试从/etc/lets encrypt/renewal/loadbalancing.xyz . conf 续订证书(load balancing . XYZ)时出现意外错误:手动插件不起作用；您现有的配置可能有问题。*

*错误为:PluginError('在以非交互方式使用手动插件时，必须使用- manual-auth-hook 提供身份验证脚本。').跳绳。"*

看来更新的唯一方法是手动重做整个程序，我认为这是不可持续的。幸运的是，有人用 GitLab 找到了一种自动更新的方法，我将在下一篇文章中更新我的尝试经历。