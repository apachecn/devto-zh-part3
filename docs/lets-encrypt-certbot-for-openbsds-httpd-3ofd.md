# 让我们加密:Certbot For OpenBSD 的 httpd

> 原文：<https://dev.to/nabbisen/lets-encrypt-certbot-for-openbsds-httpd-3ofd>

**封面图片由 [markusspiske](https://pixabay.com/en/users/markusspiske-670330/) 原创，经过精心编辑。*

## [T1】简介](#intro)

[让我们加密](https://letsencrypt.org/)是“一个免费的、自动化的、开放的认证机构”。
[Certbot](https://certbot.eff.org/) 是“一个易于使用的自动客户端，为您的 web 服务器获取和部署 SSL/TLS 证书”，被誉为“官方的 Let's Encrypt client”。

我清楚地记得，当我在 2015 年读到 Let's Encrypt 的[“我们的第一个证书现已上线”](https://letsencrypt.org/2015/09/14/our-first-cert.html)时，我感到多么兴奋。
多么精彩[他们](https://letsencrypt.org/about/)的目标是；它将“为人们提供他们需要的数字证书，以便免费启用网站 HTTPS (SSL/TLS)”，“创建一个更加安全和尊重隐私的网络”！
从 2018 年开始，他们甚至开始支持 [ACME v2 和通配符证书](https://community.letsencrypt.org/t/acme-v2-and-wildcard-certificate-support-is-live/55579)！

嗯，在 OpenBSD 和其他操作系统中，有他们的帮助很容易也很舒服😊

### 环境

*   操作系统:OpenBSD 6.4 amd64
*   Web 服务器:OpenBSD 的 httpd
*   认证:用 Certbot 0.27 加密吧

### 引用

*   [OpenBSD 的 httpd](https://obsd.solutions/en/blog/2018/10/05/openbsd-httpd-63-web-server/index.html)

## 如何安装 Certbot

只是:

```
#  pkg_add certbot 
```

Enter fullscreen mode Exit fullscreen mode

## 如何获取证书

首先，我们准备一个目录作为 webroot:

```
#  mkdir /var/www/%your-webroot% 
```

Enter fullscreen mode Exit fullscreen mode

接下来编辑 [`/etc/httpd.conf`](https://man.openbsd.org/httpd.conf.5) 为了搭建一个`%your-domain%`的 web 服务器监听 Let's Encrypt 的 HTTP 挑战进行认证:

```
# [/etc/httpd.conf] 
types {
        include "/usr/share/misc/mime.types"
}

ext_addr = egress

# ... 
cert_domain = %your-domain%
server $cert_domain {
        listen on $ext_addr port 80
        root "/%your-webroot%"
        directory auto index
} 
```

Enter fullscreen mode Exit fullscreen mode

然后重新加载配置:

```
#  rcctl restart httpd 
```

Enter fullscreen mode Exit fullscreen mode

好的。我们准备好了。

*(*你不需要暂时停止 web 服务器给[释放端口 80 给`--standalone`](https://certbot.eff.org/docs/using.html#standalone) 感谢`--webroot`！*)*

让我们尝试获取一个新证书:

```
#  certbot certonly --webroot -w /var/www/%your-webroot% -d %your-domain% 
```

Enter fullscreen mode Exit fullscreen mode

**注:根据[手册](https://certbot.eff.org/docs/using.html) :* ，上述子命令和选项的含义如下

| 子命令/选项 | 描述 |
| --- | --- |
| `certonly` | 获取或续订证书，但不要安装它。 |
| `--webroot` | 将文件放在服务器的 webroot 目录中进行身份验证。 |
| `--webroot-path WEBROOT_PATH`，`-w WEBROOT_PATH` | Webroot / `public_html`路径[s 单独]。 |
| `-d DOMAIN`，`--domains DOMAIN` | 单独指定的域。 |

此外，可能会省略`--webroot -w /var/www/%your-webroot%`，因为稍后会询问您(因此它成为非自动过程)。

**注意:如果您是第一次，系统会询问您的电子邮件地址，CA(证书颁发机构)会将通知电子邮件发送到该地址。或者，您可以在命令行上使用`-m EMAIL, --email EMAIL`选项。*

总之，`certbot`输出是这样的:

```
#  certbot certonly --webroot -w /var/www/%your-webroot% -d %your-domain%
 Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator webroot, Installer None
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for %your-domain%
Using the webroot path /var/www/%your-webroot% for all unmatched domains.
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/%your-domain%/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/%your-domain%/privkey.pem
   Your cert will expire on 20??-??-??. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le 
```

Enter fullscreen mode Exit fullscreen mode

我们收到甜蜜的“祝贺！”消息。
让我们来反映一下`/etc/httpd.conf` :
中的`fullchain.pem`和`privkey.pem`

```
# [/etc/httpd.conf] 
types {
        include "/usr/share/misc/mime.types"
}

ext_addr = egress

# ... 
#cert_domain = %your-domain%
#server $cert_domain {
#        listen on $ext_addr port 80
#        root "/%your-webroot%"
#        directory auto index
#} 
server "%your-domain%" {
        listen on $ext_addr port 80
        block return 301 "https://$SERVER_NAME$REQUEST_URI"
}
server "%your-domain%" {
        listen on $ext_addr tls port 443
        tls {
                certificate     "/etc/letsencrypt/live/%your-domain%/fullchain.pem"
                key             "/etc/letsencrypt/live/%your-domain%/privkey.pem"
        }
        root "/%your-webroot%"
        directory auto index
} 
```

Enter fullscreen mode Exit fullscreen mode

然后:

```
# rcctl restart httpd 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们获得了 web 服务器来创建 HTTPS 连接，只要它最后被访问！

### 如何管理获得的证书

请记住 [Let's Encript 的证书有效期为 90 天](https://letsencrypt.org/docs/faq/)以及[为什么是](https://letsencrypt.org/2015/11/09/why-90-days.html)。

这些是`certbot`的基本子命令:

| 子命令 | 描述 |
| --- | --- |
| 证书 | 列出 Certbot 管理的证书 |
| [更新](https://certbot.eff.org/docs/using.html#renewing-certificates) | 更新所有证书(或用`--cert-name`指定的证书) |
| 删除 | 清理与证书相关的所有文件 |
| [撤销](https://certbot.eff.org/docs/using.html#revoking-certificates) | 撤销用`--cert-path`或`--cert-name`指定的证书 |

##### 为列表

```
#  certbot certificates
Saving debug log to /var/log/letsencrypt/letsencrypt.log

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Found the following certs:
  Certificate Name: %your-domain%
    Domains: %your-domain%
    Expiry Date: 20??-??-?? ??:??:??+00:00 (VALID: 90 days)
    Certificate Path: /etc/letsencrypt/live/%your-domain%/fullchain.pem
    Private Key Path: /etc/letsencrypt/live/%your-domain%/privkey.pem
  Certificate Name: ... 
```

Enter fullscreen mode Exit fullscreen mode

##### 更新

简单的用法是这样的“更新任何以前获得的在 30 天内到期的证书”:

```
#  certbot renew 
```

Enter fullscreen mode Exit fullscreen mode

前/后挂钩可用:

```
#  certbot renew --pre-hook "rcctl stop httpd" --post-hook "rcctl start httpd" 
```

Enter fullscreen mode Exit fullscreen mode

##### 删除

完全删除证书:

```
#  certbot delete --cert-name %your-domain% 
```

Enter fullscreen mode Exit fullscreen mode

<center>✿ ✿ ✿</center>

快乐服务🌵