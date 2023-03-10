# 让我们通过 DNS 挑战加密服务器证书

> 原文：<https://dev.to/michaeldscherr/lets-encrypt-ssl-certificate-via-dns-challenge-8dd>

您是否想过在迁移网站之前是否可以获得一个`Server Certificate`？我总是假设在`certificate validation`发生之前`DNS`必须被切换*。我发现有更多的选择。*

## 场景

我的公司目前正在将我们所有的客户生产站点迁移到新的服务器上。这项工作的一部分是确保所有网站都放在`https`上。

## 服务器证书挑战

> Let's Encrypt 提供域验证证书，这意味着他们必须检查证书请求是否来自实际控制该域的人。他们通过向客户端发送一个唯一的令牌，然后发出一个 web 或 DNS 请求来检索从该令牌派生的密钥。

获得`server certificate`有两个主要选项:

*   `HTTP Challenge` -在网站的指定位置张贴指定的`file`
*   `DNS Challenge` -在域名系统中发布指定的`DNS record`

### HTTP 挑战

这通常通过在您的`web root`中的`.well-known`目录内添加一个`token`来处理。`Certbot`然后可以确认你实际控制了指定域上的资源，并且会签署一个`certificate`。

### DNS 挑战

这种方法要求您为每个请求的域添加特定的`DNS TXT`条目。当您还没有切换`DNS`，但是想要提前发出一个`certificate`(用于测试)时，这很有用。

有关挑战的更多信息，请访问 [certbot 的文档](https://certbot.eff.org/docs/challenges.html)。

## 设置

*我们将在本文的剩余部分讨论`DNS Challenge`方法。*

在下面的例子中，我将使用`Apache` & `Ubuntu 16.04`跟随[这个向导](https://certbot.eff.org/lets-encrypt/ubuntuxenial-apache)。要查找特定网络服务器/操作系统的文档，请访问 [certbot 的主页](https://certbot.eff.org/)。

首先，我们需要安装`certbot`以及所有必要的依赖项。

```
# run as root

apt-get update \
    && apt-get install software-properties-common \
    && add-apt-repository -y universe \
    && add-apt-repository -y ppa:certbot/certbot \
    && apt-get update \
    && apt-get install -y python-certbot-apache 
```

## 配置

我不会在这里讨论通配符域名，但它们是一个选项。参考[他们的文档](https://certbot.eff.org/)。

我们现在需要告诉`certbot`我们想为哪些域名发布一个`certificate`。记得单独添加每个子域。

由于每个服务器可能有多个`vhosts`，我们决定使用`--manual` & `certonly`标志。

```
# run as root

# replace with your domain
# add all relevant subdomains
certbot --manual --preferred-challenges dns certonly \
    -d yourwebsite.com \
    -d www.yourwebsite.com \          # don't forget www binding
    -d staging.yourwebsite.com \      # example subdomain
    -d staging.stage1.yourwebsite.com # example long subdomain 
```

### 挑战提示

一旦您运行上面的命令，它将提示您为每个指定的域添加一个`DNS TXT`记录。会是这样的:

```
Please deploy a DNS TXT record under the name
_acme-challenge.yourwebsite.com with the following value:

[random-string-of-characters]

Before continuing, verify the record is deployed.
(This must be set up in addition to the previous challenges; do not remove,
replace, or undo the previous challenge tasks yet. Note that you might be
asked to create multiple distinct TXT records with the same name. This is
permitted by DNS standards.)

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
```

现在重要的部分来了。您需要从每个`record name`中移除`base url`，就像这样:

| 从 certbot 请求的名称 | 要添加到`DNS`中的实际名称 |
| --- | --- |
| `_acme-challenge.yourwebsite.com` | `_acme-challenge` |
| `_acme-challenge.www.yourwebsite.com` | `_acme-challenge.www` |
| `_acme-challenge.staging.yourwebsite.com` | `_acme-challenge.staging` |
| `_acme-challenge.staging.stage1.yourwebsite.com` | `_acme-challenge.staging.stage1` |

一旦您添加了`DNS TXT`记录，并在每个挑战提示中分别点击`Continue`，验证应该会通过。

```
Press Enter to Continue
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/yourwebsite.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/yourwebsite.com/privkey.pem
   Your cert will expire on 2019-04-24\. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le 
```

## 更新你的 VHost

现在我们有了一个有效的证书，我们可以更新我们的`vhost` :

```
# inside the 443 binding

SSLEngine On
SSLCertificateFile /etc/letsencrypt/live/yourwebsite.com/cert.pem
SSLCertificateKeyFile /etc/letsencrypt/live/yourwebsite.com/privkey.pem
SSLCertificateChainFile /etc/letsencrypt/live/yourwebsite.com/chain.pem 
```

然后重启`Apache`:T1

```
sudo apachectl configtest

# if syntax ok
sudo apachectl restart 
```

## 奖励:设置自动续费

我们将利用`root`用户的`crontab`来自动更新即将到期的证书。

```
# run as root

# edit the crontab
crontab -e 
```

然后在底部加上下面两行:

```
# every Monday at 2:30am
30 2 * * 1 /usr/bin/certbot renew --deploy-hook "service apache2 reload" >> /var/log/letsencrypt/le-renew.log 
```

`certbot`将尝试每周更新一次标记为要更新的证书。然后，我们使用`--deploy-hook`仅在必要时重新加载`apache`。

特别感谢[丹尼尔·麦卡尼](https://dev.to/cpu)更新了`crontab`代码。

[![cpu image](img/7398a53767a6d32278dcdb7bbd2ff0ba.png)](/cpu)

## [丹尼尔·麦卡尼](/cpu)

[I live in the woods and help write the free software that powers Let's Encrypt.](/cpu)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)CPU](https://twitter.com/cpu)[![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)CPU](https://github.com/cpu)[![external link icon](img/7ad9ad23055d49c106b927d92662ca16.png)https://binaryparadox.net](https://binaryparadox.net)