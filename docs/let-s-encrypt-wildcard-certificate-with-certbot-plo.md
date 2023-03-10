# Letsencrypt 通配符 Let's Encrypt:带证书的通配符证书

> 原文：<https://dev.to/nabbisen/let-s-encrypt-wildcard-certificate-with-certbot-plo>

**封面图片由[openclipbart-Vectors](https://pixabay.com/users/OpenClipart-Vectors-30363/)原创，经过精心编辑。*

* * *

## 简介

[让我们加密](https://letsencrypt.org/)支持[通配符证书](https://letsencrypt.org/docs/faq/#does-let-s-encrypt-issue-wildcard-certificates)通过 ACMEv2 使用[DNS-01 挑战](https://letsencrypt.org/docs/challenge-types/#dns-01-challenge)，2018 年 3 月 13 日[开始](https://letsencrypt.org/2017/07/06/wildcard-certificates-coming-jan-2018.html)。其客户端
[Certbot](https://certbot.eff.org/) 提供`--manual`选项给[执行](https://certbot.eff.org/faq/#does-let-s-encrypt-issue-wildcard-certificates)。我真诚地感谢他们。

我写了如何用 Certbot 生成通配符证书。这是一个非常简单的任务:)
除此之外需要做的就是将 Certbot 指定的 TXT 记录添加到 DNS 服务器。

*注意:由于[需要每 90 天更新一次 Let's Encrypt 的证书](https://letsencrypt.org/docs/faq/#what-is-the-lifetime-for-let-s-encrypt-certificates-for-how-long-are-they-valid)，[每次更新](https://community.letsencrypt.org/t/how-to-renew-wildcard-cert-with-cert-bot-auto/60723)都需要一个新的 TXT 记录。

## 正文

#### 环境

*   OpenBSD: 6.5
*   Certbot: 0.32.0

#### 教程

命令是这样的:

```
#  certbot certonly --manual --preferred-challenges dns --server https://acme-v02.api.letsencrypt.org/directory --manual-public-ip-logging-ok -d '*.<your.domain>' -d <your.domain> 
```

Enter fullscreen mode Exit fullscreen mode

确保引用`*.<your.domain>`以避免错误“未找到匹配:*。< your.domain >”。

**注意:`-d *.<your.domain>`和`-d <your.domain>`的顺序似乎无关紧要。*

下面是`certonly`选项与单个证书的对比:

```
- --webroot -w <dir-path> + --manual --preferred-challenges dns --server https://acme-v02.api.letsencrypt.org/directory --manual-public-ip-logging-ok 
```

Enter fullscreen mode Exit fullscreen mode

此外，其命令行选项的详细信息是[这里是](https://certbot.eff.org/docs/using.html#certbot-command-line-options)。

然后，命令回复:

```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator manual, Installer None
Obtaining a new certificate
Performing the following challenges: dns-01 challenge for <your.domain>   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please deploy a DNS TXT record under the name _acme-challenge.<your.domain>  with the following value:
  <acme-challenge-value>   Before continuing, verify the record is deployed.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue 
```

Enter fullscreen mode Exit fullscreen mode

配置 DNS 服务器以向您的域添加记录。
记录是这样的:

| 在哪里 | 什么 |
| --- | --- |
| 类型 | 文本文件（textfile） |
| 名字 | `_acme-challenge` |
| 价值 | <acme-challenge-value>上面的命令显示</acme-challenge-value> |

注意:你可能要在这里等一会儿，等待 DNS 传播。

然后按回车。

```
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
 /etc/letsencrypt/live/<your.domain>/fullchain.pem
 Your key file has been saved at: /etc/letsencrypt/live/<your.domain>/privkey.pem
 Your cert will expire on 2019-08-01\. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le 
```

Enter fullscreen mode Exit fullscreen mode

完成: )

## 结论

因此，我得到了我的通配符证书，并应用于我的几个服务器。今天它运行得很好，我的工作量也减少了，但安全性几乎没变。

如果您正在搜索如何将 Let's Encrypt 的证书应用到 OpenBSD httpd 服务器，我在这里写道:

[![nabbisen](img/6759a36884202f6cf21b258b1e80383e.png)](/nabbisen) [## 让我们加密:Certbot For OpenBSD 的 httpd

### nabbisen Dec 14 ' 184 分钟读数

#security #letsencrypt #certbot #openbsd](/nabbisen/lets-encrypt-certbot-for-openbsds-httpd-3ofd)

感谢您的阅读。祝安全愉快。